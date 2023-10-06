---
title: "distrobox"
date: 2023-08-01
tags:
- points
---
The official documentation states,
*Use any Linux distribution inside your terminal. Enable both backward and forward compatibility with software and freedom to use whatever distribution you’re more comfortable with. Distrobox uses podman or docker to create containers using the Linux distribution of your choice. The created container will be tightly integrated with the host, allowing sharing of the HOME directory of the user, external storage, external USB devices and graphical apps (X11/Wayland), and audio.*

## Issues
### Distrobox not starting due to follwing error:
```bash
distrobox Error: unable to start container  crun: setrlimit `RLIMIT_NPROC`: Operation not permitted: OCI permission denied
```

**To Fix:**
```bash
distrobox create -c ros ros-backup
podman rm ros
podman rename ros-backup ros
distrobox enter ros
```
