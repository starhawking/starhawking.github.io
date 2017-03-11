---
title: Linux and OpenStack Notes
layout: page
permalink: "/Misc_Notes"
---

# Miscelanious Linux and OpenStack Notes

## Linux Networking Notes

#### Handy options for tcpdump:

```shell
tcpdump -nlei $interface
```

#### Check on the status of HAProxy:

Gather a variety of infornation from HAProxy:

```shell
echo "show info;show stat;show table" | socat /var/lib/haproxy/stats stdio
```

Cleanly print statistics for a few columns:

```shell
echo "show stat" | socat /var/lib/haproxy/stats stdio | awk 'BEGIN{FS=","} {sub(/^#\ */, "", $0); print $1,$2,$9,$10}' | column -t
```

List out all available columns for easy reference:

```shell
echo "show stat" | socat /var/lib/haproxy/stats stdio | awk 'BEGIN {FS=","} NR == 1 {sub(/^#\ */, "", $0); for (i=1; i < NF; i++) { print "$"i" = "$i }}'
```

Additional details for each column can be found in the HAProxy documentation
[http://www.haproxy.org/download/1.5/doc/configuration.txt](http://www.haproxy.org/download/1.5/doc/configuration.txt)

#### Pretty ip link details and statistics output:

```shell
ip -s -d -o link | sed G | tr '\\' '\n'
```

#### Check what Interface pairs up with a veth interface:

```shell
ethtool -S $vethDevice
```
This ID will match up with the entry number in the output of ip link or ip addr

An example of using this to match up a veth interface in a namespace to its corresponding veth interface in another namespace:

```shell
root@node-1:~# ip netns exec haproxy ip -d link show dev b_public
36: b_public: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 32:cc:0c:f3:ea:34 brd ff:ff:ff:ff:ff:ff promiscuity 0
    veth
root@node-1:~# ip netns exec haproxy ethtool -S  b_public
NIC statistics:
     peer_ifindex: 37
root@node-1:~# ip -o -d link | grep "^37"
37: v_public: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master br-ex state UP mode DEFAULT group default qlen 1000\    link/ether d2:06:96:a4:39:18 brd ff:ff:ff:ff:ff:ff promiscuity 1 \    veth
root@node-1:~#
```

#### List all Network Namespaces without using the ip command:

```shell
ls /var/run/netns
```

#### Find the Inode of a process’s Network Namespace:

```shell
readlink   /proc/{0..9}*/ns/net  | sort | uniq
```

#### Find the Inode of a Network Namespace:

```shell
stat /var/run/netns/*
```

## OpenStack Networking Notes

#### Quickly find what router is associated with a Network from the DB

I have yet to find a quick way to identify which Neutron Router is associated with a given Neutron Network ID, so I've assembled the following query to speed up this process:

```SQL
SELECT router_id,port_id,network_id  FROM ports t1 INNER JOIN routerports t2 ON t1.id = t2.port_id where network_id="NETWORK_UUID";
```

This query can easily be called in bash with the following function:

```shell
network_to_router() { mysql neutron -e "SELECT router_id,port_id,network_id  FROM ports t1 INNER JOIN routerports t2 ON t1.id = t2.port_id where network_id=\"$1\";" ; }
```

## OpenStack Client Notes

#### Easier Python Bindings

The standard OpenStack Python bindings are painful to use. Python Shade is so much easier.
[Python Shade](https://pypi.python.org/pypi/shade)

#### Using clouds.yaml

An easier way to define configuration options for the CLI and Python Shade bindings. No more exporting environment variables!
[clouds.yaml](https://docs.openstack.org/developer/python-openstackclient/man/openstack.html#cloud-configuration)
