---
title: Mender Client
taxonomy:
    category: docs
---

## `Secure Boot forbids loading module` during GRUB boot after a Mender update

The `mender-convert` GRUB integration for Mender does not support user configuration
for `GRUB_BACKGROUND`, `GRUB_THEME` nor `GRUB_FONT`.

<!--AUTOVERSION: "version % and"/ignore -->
`mender-convert` version 4.3.0 and earlier versions had an incomplete integration that caused that a device
configured for Secure Boot will throw these kind of warnings when booting after
a Mender update. These warnings do not compromise the security of Secure Boot.

## `mender: command not found` error when using the `mender-artifact` snapshot feature

This is caused by using an outdated version of the `mender-artifact` tool (e.g from the upstream Debian or
Ubuntu APT repositories) which cannot detect which version of the Mender Client the device is running.

To solve this issue, install `mender-artifact` from the Mender APT repository following the instructons in
the [downloads section](../../11.Downloads/docs.md#mender-artifact).

## `fsck` error when creating a Mender Artifact using the snapshot feature

When running `mender-artifact write -f ssh://...` command to create a Mender Artifact from a virtual
device, you can get the following error:

```
runFsck error: fsck error: exit status 12
```

Recent releases of the virtual device use new filesystem features, which requires up to date
filesystem tools (namely `e2fsck`) on the host. Debian 12, Ubuntu 24.04 and newer OSs work correctly.

Workaround the issue by executing a few extra steps:

On the shell of the device execute:
```
mkfifo /data/mender/dump
mender-snapshot dump > /data/mender/dump
```

The last command will not terminate, but will wait for you to execute commands from the host. So back in the shell on the host where you have the variables form the Get Started guide, run:

```
ssh -p 8822 root@${IP_ADDRESS} cat /data/mender/dump > rootfs.ext4
mender-artifact write rootfs-image \
    -f ssh://"${USER}@${IP_ADDRESS}" \
    -t "${DEVICE_TYPE}" \
    -n system-v1 \
    -o system-v1.mender \
    -f rootfs.ext4
```

While the command is running, you should see a progress bar in the device window.

From here, upload the `system-v1.mender` and continue with the rest of the steps in the guide.


## `mender-client` package have been kept back

<!--AUTOVERSION: "Mender Client version %"/ignore "mark back the %"/ignore "`mender-client` package to version %"/ignore "package to the %"/ignore "it to the % version"/ignore -->
After releasing the Mender Client version 4.0.0, we realized a critical issue with the Debian
packaging. Therefore, we decided to revert it and mark back the 3.5.2 release as the latest version
in the APT repository. If you upgraded the `mender-client` package to version 4.0.0 on a device
during that time, APT will keep the `mender-client` package to the 4.0.0 version and won't downgrade
it to the 3.5.2 version by default.

To solve the issue, either:
* Run `apt-get install mender-client`
* Run again the [express installation script](../../11.Downloads/docs.md#Express-installation)

## Removed previous stable APT repositories

We [removed](../../11.Downloads/docs.md#Set-up-the-APT-repository) the previously deprecated stable APT repository:

```
deb [arch=your-arch] https://downloads.mender.io/repos/debian stable main
```

If you see errors of the form:

```
Err:3 https://downloads.mender.io/repos/debian stable InRelease
  403  Forbidden [IP: 52.222.214.71 443]
```

it means you are using the old repository, please update to the current one (see [Set up the APT repository](../../11.Downloads/docs.md#Set-up-the-APT-repository) section).

In order to remove the obsolete repository `deb [arch=your-arch] https://downloads.mender.io/repos/debian stable main`
you can use the following command:

```bash
add-apt-repository -r "deb [arch=$(dpkg --print-architecture)] https://downloads.mender.io/repos/debian stable main"
```

and then re-add the new one with:

```bash
add-apt-repository "deb [arch=$(dpkg --print-architecture)] https://downloads.mender.io/repos/debian debian/bookworm/stable main"
```

## Repository 'http://raspbian.raspberrypi.org/raspbian buster InRelease' changed its 'Suite' value from 'stable' to 'oldstable'

In case you see the following errors during `apt-get update`:

```
E: Repository 'http://raspbian.raspberrypi.org/raspbian buster InRelease' changed its 'Suite' value from 'stable' to 'oldstable'
N: This must be accepted explicitly before updates for this repository can be applied. See apt-secure(8) manpage for details.
```

In order to accept the change of the suite name, you need to run:

```
apt-get update --allow-releaseinfo-change
```

## Raspberry Pi boot `Waiting for root device ${mender_kernel_root}...` after using `mender-convert`

Depending on the release of Raspbian in use and eventually applied package updates, booting can
block on the step `Waiting for root device ${mender_kernel_root}...`.

This can be mitigated by adding the following line in `config.txt` on the sd cards boot partition, respectively uncommenting if it is already is prepared:
`arm_64bit=0`.

## Raspberry Pi not booting / infinite reboots after flashing card with Raspberry Pi Imager

The Raspberry Pi Imager can be instructed to pre-configure the flashed image, for example
- SSH
- WiFi
- user credentials
- ...

This is done by injecting a `firstrun.sh` script together with the statement `systemd.run_success_action=reboot` on the command line. On a menderized image, this will loop forever.

To solve this problem, either reflash the image without adding customizations, or modify the `cmdline.txt` file on the boot partition of the file, removing the above `systemd` parameter.

<!--AUTOVERSION: "mender-client %"/ignore -->
## Installation of the mender-client 3.2.0 Debian package on Debian Bullseye and Ubuntu 20.04

<!--AUTOVERSION: "The mender-client version %"/ignore -->
The mender-client version 3.2.0 Debian package is deprecated. If you are
getting installation errors, with a missing
[libffi6](https://sourceware.org/libffi/) dependency, then please install the
new Debian package, as per the installation instructions in
[downloads](../../11.Downloads/docs.md#mender-client)


## Obtaining client logs

Logs are usually needed in order to diagnose an issue.

The Mender Client by default logs to the system log using `systemd`, so the easiest way to retrieve logs
is to run the following command:

```
journalctl -u mender-updated
```

Please note that the default log level is Info. It is possible to increase the
verbosity by editing the Mender systemd unit file and add the `--log-level debug` option:

```
ExecStart=/usr/bin/mender-update --log-level debug daemon
```

<!--AUTOVERSION: "versions of the Mender Client older than %"/ignore-->
!!! Note that in versions of the Mender Client older than 4.0.0, the binary and systemd service are called
!!! `mender` and `mender-client`, respectively.


### Deployment log files

In addition to system logging, Mender also writes debug logs directly to a file when
a deployment starts. This file in turn gets uploaded to the server if the
deployment fails.

By default, log files for the past 5 deployments are kept.
They are stored in `/var/lib/mender/`, named by the deployment id,
for example `deployments.0001.fcd8bca2-6dae-488e-969e-23559c674ba5.log`.


### Current status

In order to see what the Mender Client is doing currently, follow the log
as it is being written with this command:

```
journalctl -u mender-updated -f
```

<!--AUTOVERSION: "versions of the Mender Client older than %"/ignore-->
!!! Note that in versions of the Mender Client older than 4.0.0, the systemd service is called `mender-client`.

To stop it use Ctrl+C.


## Certificate expired or not yet valid

The Mender Client can not connect to the server, typically the first time it tries, and emits messages like the following to the `mender-authd` log or syslog at the device:

```
... level=info msg="Mender state: authorize-wait -> bootstrapped" module=mender
... level=error msg="authorize failed: transient error: authorization request failed: failed to execute authorization request:
Post https://<SERVER-URI>/api/devices/v1/authentication/auth_requests: x509: certificate has expired or is not yet valid" module=state
```

This could occur in several places, and the distinguishing message is **x509: certificate has expired or is not yet valid**.
Each TLS certificate has a validity period, *Not Before* and *Not After*, and this message means that the Mender Client concludes that
the current time is outside this range.

Most commonly this is caused by **incorrect time setting on the device** which runs the Mender Client. Check this by
running `date` on the device, and make sure it is correct. Consult the section on [Correct clock](../../05.Operating-System-updates-Yocto-Project/01.Overview/docs.md#correct-clock)
for a more detailed discussion.

To determine the status of your time synchronization, execute the following:

```bash
# timedatectl status
```

Note that it can take some time after boot before the time
synchronization is completed.
This can be avoided by ensuring that time synchronization is complete prior to
mender services starting. This could be achieved by adjusting mender client
services like `mender-updated`, `mender-authd`, and `mender-connect` setting
`Wants` and or `After` to include
[time-sync.target](https://www.freedesktop.org/software/systemd/man/latest/systemd.special.html#time-sync.target)
or using `ExecStartPre` to execute a command that will synchronize time or sleep
the amount of time it normally takes for the time synchronization to occur.
If after 5-10 minutes, the time still has not synchronized, consult with your
local network administrator for further troubleshooting.

If this is not the problem, you need to verify that the certificates you are using are valid.
Replace the hostname with the one for your Mender API Gateway below and run the following command:

```bash
echo | openssl s_client -connect mender.example.com:443 2>/dev/null | openssl x509 -noout -dates
```
> ```
> notBefore=Dec 14 19:52:46 2016 GMT
> notAfter=Dec 12 19:52:46 2026 GMT
> ```

Also note that the storage proxy has its own certificate, and it runs on the same host as the API Gateway
on port 9000 by default. Adjust the hostname and verify the validity of its certificate with the following command:

```bash
echo | openssl s_client -connect s3.example.com:9000 2>/dev/null | openssl x509 -noout -dates
```
> ```
> notBefore=Dec 14 19:52:46 2016 GMT
> notAfter=Dec 12 19:52:46 2026 GMT
> ```

We can see that both these certificates are currently valid.
Also see the [documentation on certificates](../../08.Server-installation/01.Overview/02.Certificates-and-keys/docs.md) for an
overview and description on how to generate new certificates.


## Certificate signed by unknown authority

The Mender Client can not connect to the server, typically the first time it tries, and emits messages like the following to the `mender-authd` log or syslog at the device:

```
... level=info msg="Mender state: authorize-wait -> bootstrapped" module=mender
... level=error msg="authorize failed :transient error :authorisation request failed: failed do execute authorisation request:
Post https://<SERVER-URI>/api/devices/v1/authentication/auth_requests: x509: certificate signed by unknown authority" module=state
```

This could occur in several places, and the distinguishing message is **x509: certificate signed by unknown authority**.
The message shows that the Mender Client rejects the Mender Server's certificate because it does not trust the certificate
authority (CA).

If your server is using a certificate that is signed by an official Certificate Authority, then you likely
need to update your client's root certificate store. For example, [hosted Mender](https://hosted.mender.io?target=_blank)
uses an official CA so the only reason your client would reject this is if it does not have updated root certificates
in its system store.

!!! Hosted Mender is available in multiple [regions](/12.General/00.Hosted-Mender-regions/docs.md) to connect to. Make sure you select your desired one before proceeding.

On the other hand, if you set up the Mender Server yourself as described in
[Production installation](../../08.Server-installation/04.Production-installation-with-kubernetes/docs.md) and generated certificates as part of it,
your need to make sure that the server certificates are in `/etc/mender/server.crt` on your device.

To test that they match, run `cat /etc/mender/server.crt` on your device, and compare that to the output
of the following command, adjusting the hostnames mender.example.com / s3.example.com (ideally run on device, but can be run from elsewhere as well):

```
openssl s_client -showcerts -connect mender.example.com:443 < /dev/null 2>/dev/null | openssl x509 && openssl s_client -showcerts -connect s3.example.com:9000 < /dev/null 2>/dev/null | openssl x509
```

If these mismatch, then you need to update `/etc/mender/server.crt` on your client.
You can do this manually for testing purposes, and you should
[include the certificates in your Yocto Project build](../../05.Operating-System-updates-Yocto-Project/06.Build-for-production/docs.md#Preparing-the-server-certificates-on-the-client).

## Depth zero self-signed certificate, openssl verify rc: 18

The Mender Client detected a self-signed certificate that is the only one in the chain
and the same certificate can't be located in the trusted store. That means
the OpenSSL is unable to verify the server identity. You have to either use
another (not self-signed) certificate or include the certificate in the local trust store.

## End entity key too short, openssl verify rc: 66

The key length for the end entity in the certificate chain is too short.
This can happen in conjunction with the security level setting in the OpenSSL
configuration file, and the actual key length. First check if the security level
setting is what you want it to be. Start by locating the `openssl.cnf` file,
by running:

```bash
# openssl version -d
OPENSSLDIR: "/opt/local/etc/openssl"
```

In the above example you can find the configuration file
at `/opt/local/etc/openssl/openssl.cnf` and check the security level:

```bash
# cat `openssl version -d | sed -e 's/.*"\([^"]*\)".*/\1/'`/openssl.cnf | grep SECLEVEL
CipherString = DEFAULT@SECLEVEL=2
```
A security level of `2` is the default one on many installations nowadays.
<!--AUTOVERSION: "Starting with the Mender Client %,"/ignore-->
Starting with the Mender Client 2.4.0, the client uses OpenSSL and it is possible
that you see this error with shorter keys and certain values of security level.
You have two choices: make the keys longer, or decrease the security level.

## The Current Software installed on my device has `_INCONSISTENT` appended to it

The `_INCONSISTENT` suffix is appended to the software name on a device when the last Artifact deployment failed, and either the rollback also failed, or the particular Update Module being used has no rollback capability. As the name implies, in this case the device is in an inconsistent state, somewhere between two known states. In this case the deployment log of the last deployment may provide more information about what went wrong, and whether there is cause for concern.


## Artifact format not supported

When deploying an update with the Mender Client, you see a log message similar to the following:

```
ERRO[0001] update install failed: failed to read and install update: reader: unsupported version: 2  module=state
```

The problem here is most likely that you have built [a new version of the Artifact format](../../02.Overview/03.Artifact/docs.md#artifact-format-versions)
that your Mender Client does not support. It could also be that you are building a very old version of the
Artifact format that your new version of the Mender Client does not support.

In either case the solution is to [build a different version of the Artifact format](../../07.Artifact-creation/01.Create-an-Artifact/docs.md) that your Mender Client supports
until you have upgraded all Mender Clients and can use the corresponding latest version of the Mender Artifact format.


## The partition layout of the device is not as expected

You have the Mender binary on your device and try to trigger a rootfs update but you get output similar to the following:

```bash
mender-update install /media/rootfs-image-mydevice.mender

Mounted root does not match boot loader environment
```

-or-

```bash
mender install /media/rootfs-image-mydevice.mender

ERRO[0000] exit status 1                                 module=partitions
ERRO[0000] No match between boot and root partitions.    module=main
```

The problem here is most likely that the device does not have the [partition layout Mender expects](../../05.Operating-System-updates-Yocto-Project/01.Overview/docs.md#partition-layout). This could have happened if you just placed the Mender binary into your rootfs, but did not [reflash the entire storage device](../../05.Operating-System-updates-Yocto-Project/20.Provisioning-a-new-device/docs.md) with the `.sdimg.` file output from the [Yocto Project build](../../05.Operating-System-updates-Yocto-Project/03.Build-for-demo/docs.md). When this happens, output from `mount` and `fw_printenv` can confirm that this is the problem you are seeing. The solution is to flash your entire storage device with the `.sdimg` output from the Yocto Project build process.


## The Mender Client uses excessive network traffic even when not deploying updates

If you are using the Mender Client in demo mode, either by selecting it when running `mender setup`, or set up with the [demo layer](../../05.Operating-System-updates-Yocto-Project/03.Build-for-demo/docs.md), the Mender Client has more aggressive [polling intervals](../../03.Client-installation/07.Configuration/01.Polling-intervals/docs.md) to simplify testing.

See the documentation on [building for production](../../05.Operating-System-updates-Yocto-Project/06.Build-for-production/docs.md) and [polling intervals](../../03.Client-installation/07.Configuration/01.Polling-intervals/docs.md) to reduce the network bandwidth usage.


## Delta updates 

For more specific troubleshooting issue please look at the [troubleshooting section for the delta update module](https://hub.mender.io/t/robust-delta-update-rootfs/1144#troubleshooting-11).


### How checksums look in a working case

The delta mechanism makes use of the [Provides and Depends](../../02.Overview/03.Artifact/docs.md#provides-and-depends).

The block below shows 3 example artifacts.

```
+-------------------------------+
|Type:       rootfs-image       |
|Version:    v1                 |
|Checksum:   5bb84175           |
|                               |
|Provides                       |
|rootfs-image.checksum: 5bb84175|  -> matches the Depends for the delta
+-------------------------------+

+--------------------------------+        +--------------------------------+
|Type:       mender-binary-delta |        |Type:       rootfs-image        |
|Version:    v2                  |        |Version:    v2                  |
|Checksum:   ff532419            |        |Checksum:   b9147deb5           |
|                                |        |                                |
|Provides                        |        |Provides                        |
|rootfs-image.checksum: b9147deb5|        |rootfs-image.checksum: b9147deb5|
|                                |        +--------------------------------+
|Depends:                        |
|rootfs-image.checksum: 5bb841755|
+--------------------------------+
```

`v1`
* Version is assumed to be running on the device
* `rootfs-image` type artifact - contains the entire partition content
* Has the same `checksum` and `rootfs-image.checksum`
    * Paylod from the artifact is the same as what ends running on the device

`v2 mender-binary-delta`
* `mender-binary-delta` type artifact - contains only a delta (binary difference between two payloads)
* Can only be applied on top of a running version with a correct checksum
    * `rootfs-image.checksum: 5bb841755` defines the checksum
* `checksum` and `rootfs-image.checksum` differ
    * `checksum` - checksum of the delta payload
    * `rootfs-image.checksum` - checksum of the payload once it's running on the device

`v2 rootfs-image`
* `rootfs-image` type artifact - it contains the entire partition content
* Result in the same version as the delta once applied to the device
    * `rootfs-image.checksum: b9147deb5` - same as the `v2 mender-binary-delta`


### How to check this on the device/server/artifact?

* artifact - `mender-artifact read <mender-artifact.mender>`
* device - Run the command on the device `mender-update show-provides`
* server UI - `Releases -> Select Release -> Expand the artifact info by clicking it -> Expand Provides and Depends`

### Checking inconsistencies about the root file system checksums

The checksum shown with `mender-update show-provides` does not necessarily represent the running partition's actual checksum.

For example, if you remount the partition `rw`, change something and remount it `ro`, the checksum in the `mender-update show-provides` won't change, while the checksum of the running partition changes.

As part of your troubleshooting, you can check that the actual checksum on the device is the same as in the `Depends` field.

A general command should be something like the below.

You can identify your active root file system (`$ACTIVE_PARTITION` in the following `dd`) with a command like `lsblk`, `fdisk -l`, `mount` or similar.

The payload size of the version running on the device can be obtained from `mender-artifact read delta.mender`, check for the variable `rootfs_file_size` and use it as `$PAYLOAD_SIZE` in the following `dd`.

```bash
dd if=/dev/"$ACTIVE_PARTITION" bs=1M count=$(expr "$PAYLOAD_SIZE" / 1048576) | sha256sum -
```

#### Common root causes for having inconsistent checksums

* Check for `mount` commands inside any **state script** that you implemented. The checksum will change if the root file system gets mounted as `rw`.

* A bootloader variable can set the file system to `rw` and remount it as `ro` later. Therefore, make sure the partition is never mounted as `rw`.

* `cat /proc/cmdline` shows the parameters passed to the kernel when it started. Make sure there is no `rw` value on the output.

* In the `/etc/fstab` file make sure the root file system is mounted as `ro`. Remember that the parameter `defaults` include the value `rw`.

* In Nvidia devices using `meta-tegra` from the **OE4T** project, the variable `KERNEL_ROOTSPEC` could set the mount of the root filesystem to be read-write in an early boot stage. Check its value with `bitbake -e <image>` if in doubt.


#### `Xdelta3: target window checksum mismatch: XD3_INVALID_INPUT`

This message is caused by the root filesystem being altered, which usually is avoided by mounting read-only.
Depending on your operating system, there might be background services that modify on-disk storage in a supposedly
transparent manner, though. Those need to be either disabled completely, or configured to not operate on the
root filesystem partition.

Known services with this effect:
- `fstrim` from the `util-linux` package
