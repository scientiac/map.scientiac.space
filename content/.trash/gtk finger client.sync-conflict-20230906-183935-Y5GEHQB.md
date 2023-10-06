---
title: "gtk finger client"
date: 2023-08-12
tags:
- points
---


A gtk client to access the finger protocol easily:

## Source Code

```cpp
#include <arpa/inet.h>
#include <gtk/gtk.h>
#include <iostream>
#include <netdb.h>
#include <netinet/in.h>
#include <stdexcept>
#include <string>
#include <sys/socket.h>
#include <unistd.h>

// Class responsible for fetching Finger information
class FingerClient {
public:
    FingerClient() {
        // Initialize variables
        host = "";
        user = "";
        lastAddress = "";
    }

    std::string fetchFingerInfo() {
        try {
            // Create a socket for communication
            int clientSocket = socket(AF_INET, SOCK_STREAM, 0);
            if (clientSocket == -1) {
                throw std::runtime_error("Error creating socket.");
            }

            // Get host information
            hostent *server = gethostbyname(host.c_str());
            if (server == nullptr) {
                throw std::runtime_error("Host not found.");
            }

            sockaddr_in serverAddr;
            serverAddr.sin_family = AF_INET;
            serverAddr.sin_port = htons(79);
            serverAddr.sin_addr = *(struct in_addr *)server->h_addr;

            // Connect to the server
            if (connect(clientSocket, (struct sockaddr *)&serverAddr, sizeof(serverAddr)) == -1) {
                close(clientSocket);
                throw std::runtime_error("Error connecting to the server.");
            }

            // Send query
            std::string query = user + "\r\n";
            send(clientSocket, query.c_str(), query.size(), 0);

            // Receive response
            std::string response;
            char buffer[1024];
            while (true) {
                ssize_t bytesRead = recv(clientSocket, buffer, sizeof(buffer), 0);
                if (bytesRead <= 0) {
                    break;
                }
                response.append(buffer, bytesRead);
            }

            // Close the socket and return the response
            close(clientSocket);
            return response;
        } catch (const std::exception &e) {
            return "Error: " + std::string(e.what());
        }
    }

    std::string validateAndSetURL(const std::string &input) {
        // Check for "finger " format
        size_t fingerPos = input.find("finger ");
        if (fingerPos != std::string::npos) {
            std::string address = input.substr(fingerPos + 7);
            if (address.empty()) {
                return "The address is invalid, use finger://example.com/user or "
                       "example.com/user";
            }

            // Parse user and host
            size_t atIndex = address.find('@');
            if (atIndex != std::string::npos) {
                user = address.substr(0, atIndex);
                host = address.substr(atIndex + 1);
                lastAddress = "finger://" + address;
                return "";
            }
        }

        // Check for "://" format
        size_t schemePos = input.find("://");
        if (schemePos != std::string::npos) {
            std::string hostUser = input.substr(schemePos + 3);
            size_t slashPos = hostUser.find('/');
            if (slashPos == std::string::npos) {
                return "Invalid URL format.";
            }
            host = hostUser.substr(0, slashPos);
            user = hostUser.substr(slashPos + 1);
            lastAddress = input;
            return "";
        } else {
            // Check for "host/user" format
            size_t slashPos = input.find('/');
            if (slashPos == std::string::npos) {
                return "Invalid URL format.";
            }
            host = input.substr(0, slashPos);
            user = input.substr(slashPos + 1);
            lastAddress = "finger://" + input;
            return "";
        }
    }

    std::string getLastAddress() const { return lastAddress; }

private:
    std::string host;
    std::string user;
    std::string lastAddress;
};

// Class responsible for managing the GUI application
class FingerApp {
public:
    FingerApp() {
        // Initialize GTK
        gtk_init(NULL, NULL);

        // Create main window and layout
        window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
        vbox = gtk_box_new(GTK_ORIENTATION_VERTICAL, 10);
        input_entry = gtk_entry_new();
        response_label = gtk_label_new(NULL);
        scrolled_window = gtk_scrolled_window_new(NULL, NULL);

        // Configure window
        gtk_window_set_title(GTK_WINDOW(window), "Finger GUI");
        gtk_window_set_default_size(GTK_WINDOW(window), 600, 400);
        g_signal_connect(window, "destroy", G_CALLBACK(gtk_main_quit), NULL);
        gtk_container_set_border_width(GTK_CONTAINER(window), 10);

        // Initialize FingerClient and GUI components
        fingerClient = FingerClient();
        gtk_container_add(GTK_CONTAINER(vbox), input_entry);
        gtk_scrolled_window_set_policy(GTK_SCROLLED_WINDOW(scrolled_window),
                                       GTK_POLICY_AUTOMATIC, GTK_POLICY_ALWAYS);
        gtk_container_add(GTK_CONTAINER(scrolled_window), response_label);
        gtk_box_pack_start(GTK_BOX(vbox), scrolled_window, TRUE, TRUE, 0); 

        gtk_container_add(GTK_CONTAINER(window), vbox);

        // Connect callback for input entry activation
        g_signal_connect(input_entry, "activate", G_CALLBACK(input_entry_activated),
                         this);

        // Update initial response label
        updateResponseLabel(
            "Enter 'finger user@host' or 'finger://host/user' and press Enter.");

        // Show all GUI elements
        gtk_widget_show_all(window);
    }

    // Start GTK main loop
    void run() { gtk_main(); }

private:
    GtkWidget *window;
    GtkWidget *vbox;
    GtkWidget *input_entry;
    GtkWidget *response_label;
    GtkWidget *scrolled_window;
    FingerClient fingerClient;

    // Update the response label with text
    void updateResponseLabel(const std::string &text) {
        gtk_label_set_text(GTK_LABEL(response_label), text.c_str());
        gtk_widget_show_all(window);
    }

    // Callback function for input entry activation
    static void input_entry_activated(GtkEntry *entry, gpointer data) {
        FingerApp *app = static_cast<FingerApp *>(data);
        const gchar *input = gtk_entry_get_text(GTK_ENTRY(entry));

        // Validate input and set URL
        std::string error_message = app->fingerClient.validateAndSetURL(input);
        if (!error_message.empty()) {
            app->updateResponseLabel(error_message);
            return;
        }

        // Fetch and update response if address is changed
        if (app->fingerClient.getLastAddress() !=
            app->fingerClient.validateAndSetURL(input)) {
            std::string response = app->fingerClient.fetchFingerInfo();
            app->updateResponseLabel(response);
        }
    }
};

// Main function
int main(int argc, char *argv[]) {
    FingerApp app;
    app.run();
    return 0;
}
```

## Explanation

### The Finger Protocol 

Imagine the Finger protocol as a virtual way to introduce one computer to another. It's like asking someone, "What are you up to right now?" The Finger protocol lets one computer inquire about the current activities, contact information, or even the name of another computer. However, here's the catch: the Finger protocol transmits messages in plain text. It's like writing a personal message on a postcard – anyone who encounters it can read it. This lack of security means the Finger protocol isn't suitable for sharing sensitive information. Think of it as sending a postcard with your plans for the day written on it, allowing anyone to see what you're up to.

### Comparing Finger with HTTPS

Let's put the Finger protocol in perspective by comparing it with the modern HTTPS protocol:

- **Finger Protocol:**
    
    - **Origin:** Developed in the early days of the internet when security concerns were less prevalent.
    - **Message Security:** Transmits messages in plain text, making them vulnerable to snooping and eavesdropping.
    - **Privacy Measures:** Lacks robust security mechanisms, making it unsuitable for handling confidential or private information.
    - **Usage:** Used primarily for simple information retrieval between computers in the early internet era.
    
- **HTTPS Protocol:**
    
    - **Origin:** Evolved to address the need for secure communication in the modern digital landscape.
    - **Message Security:** Wraps messages in layers of encryption, ensuring that only intended recipients can decipher them.
    - **Security Measures:** Utilizes digital certificates to verify the identity of servers and websites, enhancing trust and security.
    - **Usage:** Widely employed for secure online transactions, protecting login credentials, and safeguarding sensitive data.

### Constructing a GUI Finger Client

In the digital realm of 2023, where modern protocols and secure connections are the norm, the idea of creating a Finger client might seem unconventional. However, the endeavor serves as a window into the past and a reminder of how far internet communication has come. Building a Finger client today isn't about practicality; it's about understanding the origins of online interaction, comparing it to modern security-focused protocols like HTTPS, and appreciating the historical context that has shaped our digital landscape. By delving into the development of a Finger client, we explore the evolution of networking and gain insights into the security measures that are now integral to our online interactions.