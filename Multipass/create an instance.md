## [Create an instance](https://multipass.run/docs/create-an-instance#heading--create-an-instance)

> See also: [`launch`](https://multipass.run/docs/launch-command), [`info`](https://multipass.run/docs/info-command)

To create an instance with Multipass, execute:

```plaintext
$ multipass launch
…
Launched: keen-yak
```

This has launched a new instance, which has been randomly named . In particular, when we run , we find out that it is an Ubuntu LTS release, namely 18.04, with 1GB RAM, 1 CPU, 5GB of disk:`keen-yak``multipass info`

```plaintext
$ multipass info keen-yak
Name:           keen-yak
State:          RUNNING
IPv4:           10.140.94.253
Release:        Ubuntu 18.04.1 LTS
Image hash:     d53116c67a41 (Ubuntu 18.04 LTS)
CPU(s):         1
Load:           0.00 0.12 0.18
Disk usage:     1.1G out of 4.7G
Memory usage:   71.6M out of 985.4M
```

## [Create an instance with a specific image](https://multipass.run/docs/create-an-instance#heading--create-an-instance-with-a-specific-image)

> See also: [`find`](https://multipass.run/docs/find-command), [`launch `](https://multipass.run/docs/launch-command), [`info`](https://multipass.run/docs/info-command)

To find out what images are available, run:

```plaintext
$ multipass find
snapcraft:core18            18.04             20201111         Snapcraft builder for Core 18
snapcraft:core20            20.04             20210921         Snapcraft builder for Core 20
snapcraft:core22            22.04             20220426         Snapcraft builder for Core 22
snapcraft:devel                               20220525         Snapcraft builder for the devel series
core                        core16            20200818         Ubuntu Core 16
core18                                        20211124         Ubuntu Core 18
18.04                       bionic            20220523         Ubuntu 18.04 LTS
20.04                       focal,lts         20220505         Ubuntu 20.04 LTS
21.10                       impish            20220309         Ubuntu 21.10
22.04                       jammy             20220506         Ubuntu 22.04 LTS
daily:22.10                 devel,kinetic     20220522         Ubuntu 22.10
appliance:adguard-home                        20200812         Ubuntu AdGuard Home Appliance
appliance:mosquitto                           20200812         Ubuntu Mosquitto Appliance
appliance:nextcloud                           20200812         Ubuntu Nextcloud Appliance
appliance:openhab                             20200812         Ubuntu openHAB Home Appliance
appliance:plexmediaserver                     20200812         Ubuntu Plex Media Server Appliance
anbox-cloud-appliance                         latest           Anbox Cloud Appliance
charm-dev                                     latest           A development and testing environment for charmers
docker                                        latest           A Docker environment with Portainer and related tools
minikube                                      latest           minikube is local Kubernetes
```

To launch an instance with a specific image, pass the image name or alias to :`multipass launch`

```plaintext
$ multipass launch kinetic
Launched: tenacious-mink
```

`multipass info` confirms that we’ve launched an instance of the selected image.

```plaintext
$ multipass info tenacious-mink
Name:           tenacious-mink
State:          Running
IPv4:           10.49.93.29
Release:        Ubuntu Kinetic Kudu (development branch)
Image hash:     5cb61a7d834d (Ubuntu 22.10)
CPU(s):         1
Load:           0.10 0.06 0.02
Disk usage:     1.4G out of 4.7G
Memory usage:   161.8M out of 971.2M
```

## [Create an instance with a custom name](https://multipass.run/docs/create-an-instance#heading--create-an-instance-with-a-custom-name)

> See also: [`launch ... --name`](https://multipass.run/docs/launch-command)

To launch an instance with a specific name, add the option to the command line:`--name`

```plaintext
multipass launch kinetic --name helpful-duck
Launched: helpful-duck
```

## [Create an instance with custom CPU number, disk, and RAM](https://multipass.run/docs/create-an-instance#heading--create-an-instance-with-custom-cpu-number-disk-and-ram)

> See also: [`launch ... --cpus ... --disk ... --memory ...`](https://multipass.run/docs/launch-command)

A custom number of CPUs, disk and RAM size is specified using the following arguments:

```plaintext
$ multipass launch --cpus 4 --disk 20G --memory 8G
Launched: giving-catfish
```

## [Create an instance with primary status](https://multipass.run/docs/create-an-instance#heading--create-an-instance-with-primary-status)

> See also: [`launch ... --name primary`](https://multipass.run/docs/launch-command)

An instance can obtain the primary status at creation time if its name is :`primary`

```plaintext
$ multipass launch kinetic --name primary
Launched: primary
```

See the [How to use the primary instance](https://multipass.run/docs/primary-instance) page for more information.

## [Create an instance with multiple network interfaces](https://multipass.run/docs/create-an-instance#heading--create-an-instance-with-multiple-network-interfaces)

> See also: [`launch ... --network`](https://multipass.run/docs/launch-command)

Multipass can [`launch`](https://multipass.run/docs/launch-command) instances with additional network interfaces, via the option. That is complemented by the [`networks`](https://multipass.run/docs/networks-command) command, to find available host networks to bridge with.`--network`

This is supported only for images with [cloud-init support for Version 2 network config](https://cloudinit.readthedocs.io/en/latest/topics/network-config-format-v2.html), which in turn requires [netplan](https://netplan.io/) to be installed. So, from 17.10 and core 16 onward, except for . And then only in the following scenarios:`snapcraft:core16`

- on Linux, with LXD
- on Windows, with both Hyper-V and [VirtualBox](https://multipass.run/docs/using-virtualbox-in-multipass-windows)
- on macOS, with the QEMU and [VirtualBox](https://multipass.run/docs/using-virtualbox-in-multipass-macos) drivers

The option can be given multiple times, each one requesting an additional network interface (beyond the default one, which is always present). Each use takes an argument specifying the properties of the desired interface:`--network`

- `name` — the only required value, it identifies the host network to connect the instance’s device to (see [`networks`](https://multipass.run/docs/networks-command) for possible values)
- `mode` — either (the default) or ; with , the instance will attempt automatic network configuration`auto``manual``auto`
- `mac` — a custom MAC address to use for the device

These properties can be specified in the format . but a simpler form with only is available for the most common use-case. Here is an example:`<key>=<value>,…``<name>`

```plaintext
$ multipass launch --network en0 --network name=bridge0,mode=manual
Launched: upbeat-whipsnake

$ multipass exec upbeat-whipsnake -- ip -br address show scope global
enp0s3           UP             10.0.2.15/24
enp0s8           UP             192.168.1.146/24
enp0s9           DOWN

$ ping -c1 192.168.1.146  # elsewhere in the same network
PING 192.168.1.146 (192.168.1.146): 56 data bytes
64 bytes from 192.168.1.146: icmp_seq=0 ttl=64 time=0.378 ms
[...]
```

In the example above, we got the following interfaces inside the instance:

- `enp0s3` — the default interface, that the instance can use to reach the outside world and which Multipass uses to communicate with the instance;
- `enp0s8` — the interface that is connected to on the host and which is automatically configured;`en0`
- `enp0s9` — the interface that is connected to on the host, ready for manual configuration.`bridge0`

### [Routing](https://multipass.run/docs/create-an-instance#heading--routing)

Extra interfaces are configured with a higher metric (200) than the default one (100). So, by default the instance will only route through them if they’re a [better match](https://en.wikipedia.org/wiki/Longest_prefix_match) for the destination IP:

```plaintext
$ multipass exec upbeat-whipsnake -- ip route
default via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100
default via 192.168.1.1 dev enp0s8 proto dhcp src 192.168.1.146 metric 200
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15
10.0.2.2 dev enp0s3 proto dhcp scope link src 10.0.2.15 metric 100
192.168.1.0/24 dev enp0s8 proto kernel scope link src 192.168.1.146
192.168.1.1 dev enp0s8 proto dhcp scope link src 192.168.1.146 metric 200

$ multipass exec upbeat-whipsnake -- ip route get 91.189.88.181
91.189.88.181 via 10.0.2.2 dev enp0s3 src 10.0.2.15 uid 1000
    cache

$ multipass exec upbeat-whipsnake -- ip route get 192.168.1.13
192.168.1.13 dev enp0s8 src 192.168.1.146 uid 1000
    cache
```

### [Bridging](https://multipass.run/docs/create-an-instance#heading--bridging)

On Linux, when trying to connect an instance network to an Ethernet device on the host, Multipass will offer to create the required bridge:

```plaintext
$ multipass networks
Name             Type      Description
eth0             ethernet  Ethernet device
lxdbr0           bridge    Network bridge
mpbr0            bridge    Network bridge for Multipass
virbr0           bridge    Network bridge

$ multipass launch --network eth0
Multipass needs to create a bridge to connect to eth0.
This will temporarily disrupt connectivity on that interface.

Do you want to continue (yes/no)?
```

However, Multipass requires to achieve this. On installations that do not have installed (e.g. Ubuntu Server), the user can still create a bridge by other means and pass that to Multipass. For instance, this configuration snippet achieves that with :`NetworkManager``NetworkManager``netplan`

```yaml
network:
  bridges:
    mybridge:
      dhcp4: true
      interfaces:
        - eth0
```

That goes somewhere in (e.g. ). After a successful or , Multipass will show the new bridge with the command and instances can be connected to it:`/etc/netplan/``/etc/netplan/50-custom.yaml``netplan try``netplan apply``networks`

```plaintext
multipass launch --network mybridge
```

Another option is to install , but other network handlers need to be deactivated to avoid conflicts and make the new bridges permanent.`NetworkManager`

## [Create an instance with a custom DNS](https://multipass.run/docs/create-an-instance#heading--create-an-instance-with-a-custom-dns)

In some scenarios the default of using the system-provided DNS will not be sufficient. When that’s the case, you can use the option to the [`launch`](https://multipass.run/docs/launch-command) command, or modify the networking configuration after the instance started.`--cloud-init`

### [The `--cloud-init` approach](https://multipass.run/docs/create-an-instance#heading--the---cloud-init-approach)

> See also: [`launch ... --cloud-init`](https://multipass.run/docs/launch-command)

To use a custom DNS in your instances, you can use this cloud-init snippet:

```yaml
#cloud-config
bootcmd:
  - printf "[Resolve]\nDNS=8.8.8.8" > /etc/systemd/resolved.conf
  - [systemctl, restart, systemd-resolved]
```

Replace with whatever your preferred DNS server is. You can then launch the instance using the following:`8.8.8.8`

```bash
$ multipass launch --cloud-init systemd-resolved.yaml
```

### [The netplan.io approach](https://multipass.run/docs/create-an-instance#heading--the-netplanio-approach)

After the instance booted, you can modify the file, adding the entry:`/etc/netplan/50-cloud-init.yaml``nameservers`

```yaml
network:
  ethernets:
    ens3:
      dhcp4: true
      match:
        macaddress: 52:54:00:fe:52:ee
     set-name: ens3
     nameservers:
       search: [mydomain]
       addresses: [8.8.8.8]
```

You can then test it:

```plaintext
$ sudo netplan try
Do you want to keep these settings?


Press ENTER before the timeout to accept the new configuration


Changes will revert in 120 seconds
...
```
