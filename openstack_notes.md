# Miscelanious Linux and OpenStack Notes

##Handy options for tcpdump:
```shell
tcpdump -nlei $interface
```

##Check on the status of HAProxy:
```shell
echo "show info;show stat;show table" | socat /var/lib/haproxy/stats stdio
```

##Pretty ip link details and statistics output:
```shell
ip -s -d -o link | sed G | tr '\\' '\n'
```

##Check what Interface pairs up with a veth interface:
```shell
ethtool -S $vethDevice
```
This ID will match up with the entry number in the output of ip link or ip addr

##List all Network Namespaces without using the ip command:
```shell
ls /var/run/netns
```

##Find the Inode of a process’s Network Namespace:
```shell
readlink   /proc/{0..9}*/ns/net  | sort | uniq
```
##Find the Inode of a Network Namespace:
```shell
stat /var/run/netns/*
```
