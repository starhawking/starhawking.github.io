---
title: OpenStack Troubleshooting Notes
layout: page
permalink: "/OpenStack_Troubleshooting"
---

# OpenStack Troubleshooting Note

A list of various items I find handy while troubleshooting OpenStack

#### Check the RabbitMQ Queues:

It is useful to be able to check which queues may be growing uncontrollably. This command lets you view only the queues with a non-zero number of messages in them, and see if this is likely because there isn't a consumer on the queue.

```shell
rabbitmqctl list_queues name consumers messages | grep -wv "0$"
```

## Useful Database Operations:

#### Check the number of security group rules per security group:

```sql
select securitygroups.id,count(distinct securitygrouprules.id) from securitygroups join securitygrouprules on securitygrouprules.security_group_id = securitygroups.id group by securitygroups.id;
```

#### Check the number of security group rules per tenant:

```sql
SELECT tenant_id,COUNT(*) AS RuleCount FROM securitygrouprules GROUP BY tenant_id;
```
