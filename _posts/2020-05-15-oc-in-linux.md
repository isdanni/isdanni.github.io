---
layout: single
title: "Setup Objective-C dev env in Ubuntu 18.04 LTS"
author: "Danni"
toc: true
toc_sticky: true
tags:
  - Linux
  - iOS
---

It is perfectly fine to run Objective-C code in Ubuntu environment since Clang is available for installation as a package for most of the Linux distributions. So if you do not have a MBP like I do, and wanna get a sense of how OC code works(but not to develop an iOS application, unfortunately, since the iOS/OS X dev stack inclduing the framework, APIs and toolings are not available for Linux), this is the starter guide for you:

**1. Install GNU Objective-C Compiler & CNU-step Dev Libraries**

Objectiv-C on Linux requires the [GNUstep package](https://packages.ubuntu.com/search?keywords=gnustep)   and the Clang compiler(Or the [GCC](https://gcc.gnu.org/)); 

```shell
sudo apt-get install gobjc gnustep gnustep-devel
```

**2. Create the first Hello, World OC program**


```shell
touch hello.m  # create the file hello with .m extension
nano hello.m # Edit the file
```

Use `Ctrl + Shift + V` the paste following code; `Ctrl + S` to save and `Ctrl + X` to exit;

```objective-c
// 'Hello World' Program in Objective-C
#import <Foundation/Foundation.h>

int main (int argc, const char * argv[])
{
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    NSLog (@"Hello, World!");
    [pool drain];
    return 0;
}
```

Now compile the code in terminal:

```shell
gcc $(gnustep-config --objc-flags) -o hello hello.m $(gnustep-config --base-libs)
```

Now, if you run ls or l again, we will see the compiled program in the same file path:

```shell
~$ l
hello*  hello.d  hello.m
```

Now run the program. We will have the output in the format of `date time exc_num file output`; Super easy and simple ;)

```shell
~$ ./hello
2020-05-15 14:12:37.659 hello[18392:18392] Hello, World! 
```


### Reference

- [question 29708566 stackoverflow](https://stackoverflow.com/questions/29708566/objective-c-environment-setup-for-ubuntu-linux)
- [Install macOS using VirtualBox in Windows(A really outdated version, but could get a sense of how it works in VM lol)](https://io9.gizmodo.com/how-to-run-mac-os-x-inside-windows-using-virtualbox-5938332)