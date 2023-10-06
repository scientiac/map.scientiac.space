---
title: Termux API
date: 2023-10-06
tags:
  - points
---
# Examples for 'termux-dialog'

### Basic Input Box

``` bash
termux-dialog -t "This is my Title" -i "Enter Info Here"
```

### Yes/No Confirm

``` bash
termux-dialog confirm -t "CONTINUE" -i "Would you want to continue?"
```

### Multi Select Checkbox

``` bash
termux-dialog checkbox -t "Which ones would you like?" -v "Option 1,Option 2,This is Option 3"
```

### Number Selector

``` bash
termux-dialog counter -t "How many?" -r 0,10
```

### Date Selector

``` bash
termux-dialog date -t "Please Select a Date" 
```

The following changes the default date if none is selected but it doesn't change the default date displayed.

``` bash
termux-dialog date -t "Please Select a Date" -d "04-07-2025"
```

### Radio Select
``` bash
termux-dialog radio -t "Please Select A Name" -v "John,Jack,Jill" 
```

Similar to radio, but slides from bottom and instant entry on select.

``` bash
termux-dialog sheet -t "Please Select A Name" -v "John,Jack,Jill" 
```

### Drop Down Menu 

``` bash 
termux-dialog spinner -t "Please Select A Name" -v "John,Jack,Jill" 
```

## More Advanced Input Boxes

### Password Entry

``` bash
termux-dialog -t "Password Needed" -i "Enter Password" -p
```

### Multi Line Input Box
``` bash
termux-dialog -t "This is my Title" -i "Enter Info Here" -m
```

### Number Entry

``` bash
termux-dialog -t "How Old Are You" -i "Enter Age Here" -n
```

### Time Entry

``` bash
termux-dialog time -t "Select A Time
```
