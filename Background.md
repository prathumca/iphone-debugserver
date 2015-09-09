# Background #

When an iPhone is connected to a mac running Xcode, it is possible to create and debug/run iphone applications from Xcode. They run in either an on board iPhone Simulator or on your physical device. The Simulator does not wrap ARM instructions but is merely compiled in i386 ELF. This limits a lot of developement, and also does nto allow access to other parts of the file system as they are simply not replicated on the mac system.

This leaves devlopement on the physical iPhone as the only option to create **useful** applications or take advantage of hardware acceleration for the flashy graphics.

The apple iPhone Developer Program (starting from US $99) allows remote testing of code. A jailbroken iPhone can do this for free. There are several steps to bypass this, both on the target Xcode hosting mac and remote iPhone.

Updated wiki's may cover this, and a bash script to automate the process may be published later.

However, on device testing leads to another, new sandbox which is relativey new (present since the SDK release) which disallows filesystem calls to all areas of the filesystem (regardless if they are writable or not); except one "application data" directory.

This is a problem with the specific code operating on the iPhone which enables the remote debugging.

# DeveloperDiskImage #

`File Path: Your Mac/Developer/Platforms/iPhoneOS.platform/DeviceSupport/{ target iPhone OS version}/DeveloperDiskImage.dmg`

and its' accompanying signature file in its' directory is the code that is executed on the iPhone. The presence of a signature file, checked by Xcode on the host mac makes editing the disk image non-trivial and non-optimal solution to the sandboxing problem.

The disk image is the root of the problem, however, and once it is on a (jailbroken) iphone, it is much more susceptible to pwnage.

The disk image is encrypted on the filesystem of the iphone after being sent to a program at Your iPhone/usr/libexec/mobile\_image\_mounter which creates a ram disk (Your iPhone/dev/disk1) that is mounted read-only at Your iPhone/Developer. Severe security checks are pertformed and if at any point fails, /Developer not empty, disk image is altered, etc, mobile\_image\_mounter locks down and returns an error to the host Xcode making alterations to /Developer impossible.

Despite being thorough on the security checks, /Developer can be unmounted and a copy of the developer disk image as regular files, allows "read-write" access to the "image" but most importantly, Xcode knows no different (still executes debugserver correctly)

# Umount Binary  for arm-apple-darwin #

The exclusion of a umount binary with the distributed terminal system of the iphone hinders the automatic unmounting process but the executable provided by this project is trivial, and allows the superuser to unmount, based on the pc-gnu-linux umount binary.

Once read-write access to the developer disk image is achieved, edition of the included developer tools is posible.
Currently, there is no way to bypass the permission denied errors, but read-write access is a good start.

# Debugserver #

The primary program in charge of controlling the  applications sent to the iPhone from the mac is a program located at Your iPhone/Developer/usr/bin/debugserver.

Not much is known about this program except that:

  * it is invoked by Xcode and remains running for the life of the application.

  * it controls the application it is debugging

  * it checks in with lockdown when the command line argument "--lockdown" is passed to it

  * its parent PID is 1 i.e. launchd launches it...

  * it is invokable from the command line and hence debuggable in user space.

  * it is believed to be the main factor involved in denying access to the full filesystem

  * it is referenced in `Your iPhone/Developer/Library/Lockdown/ServiceAgents/com.apple.debugserver.plist`  and `Your iPhone/Developer/Library/Lockdown/ServiceAgents/com.apple.debugserver.applist.plist` however changing of the arguments in these plists seems to make no difference to its arguments... perhaps Xcode caches these plists on remount of the image to the iphone (image must be remounted if the iphone has rebooted since its last checking with Xcode)

# TODO list #

To achieve this projects goal of full write access to the whole iPhone filesystem (both in an xcode debugged program and to Your iPhone/Developer from a shell these proposed goals must be reached.

  * A writable entry point to the debugserver  - **done!** _unmount the image sent to iphone_

  * A debugserver that does not checkin with the lockdownd service

by: a) patching the debugserver binary itself OR b) updating the plist 'cache' that exists somewhere between xcode and iphone, and controls the program arguments given to debugserver

# Predictions #

The patching of debugserver looks likely to be the most viable option becuase it is not even know if the binary exits (under instruction of Xcode, parent pid, itself, lockdownd, etc) when called by Xcode without --lockdown as an argument.

the reasons for this concern are  due to output printed to stdout with various arguments:

iPh:/Developer/usr/bin root# ./debugserver
debugserver-27 for armv6 Copyright (c) 2007-2008 Apple, Inc.  All Rights Reserved.
Usage: ./debugserver host:port [program-name program-arg1 program-arg2 ...]

iPh:/Developer/usr/bin root# ./debugserver --lockdown
debugserver-27 for armv6 Copyright (c) 2007-2008 Apple, Inc.  All Rights Reserved.

(infinite loop)

./debugserver 192.168.0.6:120            #target mac, rubbish port number
debugserver-27 for armv6 Copyright (c) 2007-2008 Apple, Inc.  All Rights Reserved.
Failed to get connection from a remote gdb process.

(infinite loop)

iPh:/Developer/usr/bin root# ./debugserver 192.168.0.6:120 debugserver
debugserver-27 for armv6 Copyright (c) 2007-2008 Apple, Inc.  All Rights Reserved.

(infinite loop)


It is not known what the host:port is talking about as:
> a) an nmap of the host xcode mac returns no different extra services when running debugserver
> b) the whole thing is run over USB, and wireless debugging is "not possible"


# cached plist #

even less is known about the plists, and their load time and how editing them would affect the process except: it is believed they are read first thing after the readonly disk image is mounted. nop'ing the mount system call in mobile\_image mounter has not been tried but would be a good option to test and get lucky on. that would presumably enable a custom /Developer directory and custom plists which are actually read by xcode, however i dont see that working that simply.




