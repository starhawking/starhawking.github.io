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

```shell
echo "show info;show stat;show table" | socat /var/lib/haproxy/stats stdio
```

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
