# VirtualMouse

Virtual mouse drivers for linux.
This system is most likely *overkill* for your purposes. See if [xdotool](https://github.com/jordansissel/xdotool) provides the functionality you need. xdotool provides a C library and also a [command line tool](http://manpages.ubuntu.com/manpages/trusty/man1/xdotool.1.html). Alternatively, there are many examples of input subsystem based drivers (planned for virtual mouse) for VirtualMouse which can be used.

## Build
### Driver
VirtualMouse is designed for version 5.4.x for the Linux Kernel. This kernel module is built [how any other kernel module is built](https://tldp.org/LDP/lkmpg/2.6/html/x181.html). However, for contributors or developers using a Vagrant built virtual machine the simplest method is to adapt the [VirtualHideout Vagrant Build System](https://github.com/kingpulse/VirtualHideoutVagrant) to their needs and compile using the `build.sh` script. 

The VM_DEBUG_ENABLE flag can be set to enable debug messages. This is the default in the `build.sh` script.

### Libvm
The library for VirtualMouse interaction is located in the `/interface` folder. It is built by running the Makefile. By default it runs with the flag `VM_EXPORT_GET_BUTTONS` which exports get methods for typical buttons for non-native languages which cannot access the definitions.

## X11 configuration
The X window system can be configured to use specific devices. In some cases this is necesary if the X window system does not
automatically detect the device. For example, the current BUS protocol implementation for a virtual mouse is not automatically detected by the X window system. If this is the case, the `AutoAddDevices` X server flag needs to be set to false in the X server configuration. For example:

```
Section "ServerFlags"

  Option "AutoAddDevices" "False"
  
EndSection
```

An input device for the virtual mouse needs to also be specified in the configuration. The follow example show a mouse implementing the BusMouse protocol running on the /dev/vmouse0 node with two buttons:

```
Section "InputDevice"
  Identifier "VMouse0"
  Driver "mouse"
  Option "Protocol" "BusMouse"
  Option "Device" "/dev/vmouse0"
  Option "Buttons" "2"
EndSection
```

Finally, under the `ServerLayout` section set the virtual mouse as a `CorePointer`. For example:

```
Section "ServerLayout"
  Identifier "X.org Configured"
  Screen 0 "Screen0" 0 0
  InputDevice "VMouse0" "CorePointer"
  InputDevice "Keyboard0" "CoreKeyboard"
EndSection
```

## Development environment Tips

If you would like to get a compile\_commands.json file for IDE indexing such as for Clion or YouCompleteMe use the [BEAR](https://github.com/rizsotto/Bear) utlity. This intercepts the build calls when building the linux kernel. Download the appropriate linux kernel for the Vagrantbuildsystem repository and build it 
through bear. To use the same compile configuration as used in the Vagrantbuildsystem copy the configuration file
from `/boot/config-virt` to your build folder on the host machine. Then run `make O=/buildpath oldconfig` to transform
the config. Then run `bear make O=/buildpath` to build the kernel. This will build the kernel and create a
compile_commands.json file to use for indexing :).

NB: If you are on Debian you will need to disable the CONFIG\_MODULE\_SIG\_ALL, CONFIG\_MODULE\_SIG\_KEY and CONFIG\_SYSTEM\_TRUSTED\_KEYS settings in the config file. See https://lists.debian.org/debian-kernel/2016/04/msg00579.html.
