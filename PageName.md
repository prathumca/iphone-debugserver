# Patching Debugserver #

I have been reversing and patching debugserver for long enough now, to notice that permuting a single byte yeilds this error from Xcode

`Error launching remote program: security policy error.`

which leads me to think: somewhere a CRC is performed (not on iphone, becuase executing from shell runs fine) on the host mac !!!?!?!?!?

here is the debugger output up until the error which is fatal. (xcode)

```
[Session started at 2009-06-13 21:54:12 +1000.]
Loading program into debugger…
GNU gdb 6.3.50-20050815 (Apple version gdb-963) (Tue Dec  9 06:23:51 UTC 2008)
Copyright 2004 Free Software Foundation, Inc.
GDB is free software, covered by the GNU General Public License, and you are
welcome to change it and/or distribute copies of it under certain conditions.
Type "show copying" to see the conditions.
There is absolutely no warranty for GDB.  Type "show warranty" for details.
This GDB was configured as "--host=i386-apple-darwin --target=arm-apple-darwin".tty /dev/ttys005
Program loaded.
target remote-mobile /tmp/.XcodeGDBRemote-3836-30
Switching to remote protocol
mem 0x1000 0x3fffffff cache
mem 0x40000000 0xffffffff none
mem 0x00000000 0x0fff none
sharedlibrary apply-load-rules all
run
Running…
Error launching remote program: security policy error.
Error launching remote program: security policy error.
```