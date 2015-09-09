# uNmount for iphone #

here is the basic code to unmount the /Developer disk image. This is planned to become a fully featured umount binary for distribution with pwnagetool as the base shell system.

```
#include <stdio.h>
#include <string.h>
#include <errno.h>
#include <sys/mount.h>

int main() {

  if(unmount("/Developer",0) != 0){ //note the N in the uNmount system call ; )
     printf("Error: %s",strerror(errno));
  }

  return 0;
}

```