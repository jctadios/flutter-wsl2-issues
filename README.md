# flutter-wsl2-issues
Fixed some issues encountered while using wsl2 using flutter.

### WSL Version
- [x] WSL 2
- [ ] WSL 1
 
### Kernel Version
5.10.16

#### Distro Version
Ubuntu 20.04

### Other Software
KVM

## 1. Emulator Denied Permission :warning:

Incorrect permissions on /dev/kvm is preventing startup of Android emulator although the user is a member of the required group.

```
$ emulator -avd api29
emulator: Android emulator version 30.6.5.0 (build_id 7324830) (CL:N/A)
ProbeKVM: This user doesn't have permissions to use KVM (/dev/kvm).
The KVM line in /etc/group is: [kvm:x:108:<user>]

If the current user has KVM permissions,
the KVM line in /etc/group should end with ":" followed by your username.

If we see LINE_NOT_FOUND, the kvm group may need to be created along with permissions:
    sudo groupadd -r kvm
    # Then ensure /lib/udev/rules.d/50-udev-default.rules contains something like:
    # KERNEL=="kvm", GROUP="kvm", MODE="0660"
    # and then run:
    sudo gpasswd -a $USER kvm

If we see kvm:... but no username at the end, running the following command may allow KVM access:
    sudo gpasswd -a $USER kvm

You may need to log out and back in for changes to take effect.

handleCpuAcceleration: feature check for hvf
emulator: ERROR: x86 emulation currently requires hardware acceleration!
CPU acceleration status: This user doesn't have permissions to use KVM (/dev/kvm).
The KVM line in /etc/group is: [kvm:x:108:<user>]

If the current user has KVM permissions,
the KVM line in /etc/group should end with ":" followed by your username.

If we see LINE_NOT_FOUND, the
More info on configuring VM acceleration on Linux:
https://developer.android.com/studio/run/emulator-acceleration#vm-linux
General information on acceleration: https://developer.android.com/studio/run/emulator-acceleration.
```

# :heavy_check_mark: Solution
That's expected, WSL2 doesn't have udev.

You can use wsl.conf to change those permissions at boot time with something like this though:
```
# /etc/wsl.conf
[boot]
command = /bin/bash -c 'chown root:kvm /dev/kvm && chmod 660 /dev/kvm'
```
