# macOS VM's for tstest and natlab

This utility is designed to provide custom virtual machine tooling support for macOS.  The intent
is to be able to quickly create and spin up small, preconfigured VMS, for executing integration
and unit tests.

The primary driver is to provide support for VZVirtioNetworkDeviceConfiguration which is not 
supported by other popular macOS VM hosts as well as the freedom to fully customize and script
all virtual machine setup and interaction. VZVirtioNetworkDeviceConfiguration gives us
the ability to directly inject and sink network traffic for simulating various network contitions,
protocols, and topologies and ensure that the tailscale client handles all situations correctly.

This may also be used as a drop-in replacement for UTM on ARM Macs for quickly spinning up 
test VMs.  It has the added benefit that, unlike UTM which uses AppleScript, it is can be run
via SSH.

## Components

The appliction is built in 2 components:

The tailmac command line utility is used to set up and configure VM instances.
The TailMac.app does the heavy lifting.

You will typically initiate all interactions via the tailmac command-line util.

For a full list of options:
```
tailmac -h
```


## Building

```
%make all
```

Will build both the tailmac and the VMHost app.  You will need a developer account.  The default bundle identifiers
default to tailscale owned ids, so if you don't have (or aren't using) a tailscale dev account, you will need to change this.
This should build automatically as long as you have a valid developer cert.  Signing is automatic.  The binaries both
require proper entitlements, so they do need to be signed.

There are separate recipes in the makefile to rebuild the individual components if needed.

All binaries are copied to the bin directory.

You can generally do all interactions via the tailmac command line util.

## Locations

Everything is persisted at ~/VM.bundle

Each vm gets it's own directory.  These can be archived for posterity to preseve a particular image or state.
The mere existence of a directory containing all of the required files in ~/VM.bundle is sufficient for tailmac to 
be able to see and run it.  ~/VM.bundle and it's contents is tailmac's state.  No other state is maintained elsewhere.

Each vm has it's own custom configuration which can be modified while the vm is idle.  It's simple JSON - you may
modify this directly, or using 'tailmac configure'.

## Installing

### Default a parameters

The default virtio socket device port is 51009
The default server socket for the virtual network device is /tmp/qemu.sock
The default memory size is 4Gb
The default mac address is 5a:94:ef:e4:0c:ee

### Creating and managing VMs

To create a new VM (this will grab a restore image if needed).  Restore images are large and installation takes a few minutes.
```
tailmac create --identifier my_vm_id
```

To refresh an existing restore image:
```
tailmac refresh
```

To clone an existing vm (this will clone the mac and port as well)
```
tailmac clone --identifer old_vm_id --target-id new_vm_id
```

To reconfigure a vm with a specific mac and a virtio socket device port:
```
tailmac configure --identifier vm_id --mac 11:22:33:44:55:66 --port 12345
```

## Running a VM

MacHost is an app bundle, but the main binary behaves as a command line util.  You can invoke it
thusly:

```
tailmac --identifier machine_1
 ```

 You may invoke multiple vms, but the limit on the number of concurrent instances is on the order of 2.

 To stop a running VM (this is a fire and forget thing):

 ```
 tailmac stop --identifier machine_1
 ```
