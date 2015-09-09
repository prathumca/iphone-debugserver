This project aims to patch or otherwise call the debugserver on a development iphone  and allow the debugging of programs from a remote Xcode host with full access to the filesystem and no lockdownd restrictions.


**Note: Xcode performs a CRC on debugserver EVERY TIME IT IS RUN** a patched debugserver is detectable and violates a 'security policy'


see the error in the wiki section...

Progress Map:

| /Developer Write access| <font color='#00FF00'>Yes!      </font>      |
|:-----------------------|:---------------------------------------------|
| patch for debugserver  |<font color='#FF0000'> No! </font>            |
| custom debugserver args| <font color='#00FF00'>Yes! but causes a fatal crash </font>|
| Custom /Developer disk image|<font color='#00FF00'>yes!</font>             |
|RDWR access for iphone apps| <font color='#F00F00'>No! </font>            |

note: this command stuffs up one of the million security checks xcode (iphone?? i dont know anymore...) does

> sysctl -w security.mac.proc\_enforce=0 security.mac.vnode\_enforce=0
