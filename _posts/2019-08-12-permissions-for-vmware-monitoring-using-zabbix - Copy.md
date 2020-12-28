---
layout: post
title: Permissions for VMWare monitoring using Zabbix
comments: true
---

Here is an annoying issue that I have banged my head against all day today.

Today I started adding a new vCenter to Zabbix. Zabbix has [very handy in-built templates to monitor VMWare](https://www.zabbix.com/documentation/4.2/manual/vm_monitoring){:target="_blank"}, but it is very hard to find what kind of VMWare permissions Zabbix needs to perform its duties.

<!-- more -->

It turns out that Read-Only permissions are what you need, but in vCenter there are several places where you can apply those, and that’s where I made the mistake that cost me today’s head-banging against the wall.

In vCenter, you can set a global “Read-Only” permission in:

1. vCenter -> Administration -> Global Permissions.
2. vCenter -> Hosts and Clusters -> Click on VCenter name -> Permissions tab

It turns out that #1 is completely useless for Zabbix. I am not sure this is because our vCenter is linked to another vCenter in another site or just because Global Permissions aren’t meant for that.

#2 is what you need, and you need to permission the Zabbix user for each of your vCenters. On top of that, you will need to tick “Propagate to children” when adding the relevant permission, or your Zabbix discovery will fail completely silently, without telling you that anything is wrong.


{:refdef: style="text-align: center;"}
![image](/assets/img/posts_img/2019-08-12-permissions-for-vmware-monitoring-using-zabbix/screenshot.png)
{: refdef}

