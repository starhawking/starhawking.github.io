---
title: Generic Linux Notes
layout: page
permalink: "/generic_linux_notes"
---

# Generic Linux Notes

## XFS Filesystem Repair
I've encountered situations where `xfs_check` and `xfs_repair -n` reported an unmounted filesystem as both mounted and readable. 
The easiest workaround seems to be to actually have the volume mounted, but mounted in read-only. If that doesn't work, the all-knowing search engine will need to be further consulted.

```shell
# mount -r -o remount /dev/sdbroken1
# xfs_repair -n /dev/sdbroken1
```
