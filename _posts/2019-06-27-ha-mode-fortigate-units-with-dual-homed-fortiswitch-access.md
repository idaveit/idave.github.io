---
layout: post
title: HA-mode FortiGate units with dual-homed FortiSwitch access
comments: true
---

Looking at the Fortinet documentation for FortiOS 6.0, it doesn’t say a lot about [how to set up HA-mode FortiGate units with dual-homed FortiSwitch access](https://help.fortinet.com/fos60hlp/60/Content/FortiOS/fortigate-managing-fortiswitch/Stacking.htm#HA-mode5){:target="_blank"}. It just shows a diagram, and it seems to assume that everything is clear from that diagram.

It wasn’t very clear for me, so I went ahead and asked Fortinet TAC for some help.

This is the set up that I am about to deploy in one of our sites:

![image](/assets/img/posts_img/2019-06-27-ha-mode-fortigate-units-with-dual-homed-fortiswitch-access/2019-06-27-ha-mode-fortigate-units-with-dual-homed-fortiswitch-access-diagram.png)

The diagram shows my intended set-up, with two Fortigates in HA, two “core” Fortiswitches and two “access” fortiswitches. In reality, we intend to use the core Fortiswitches for access too, not just as pure Core switches. The little numbers next to the cables are the port numbers on nearby devices.

My doubts were as follows.

**Once you set up the core layer’s ICL (Inter-Chassis Link), do you retain the ability to set up each individual switch’s ports or can you only set up MCLAGs (Multichassis LAGs)? (i.e., can I connect the two Random PCs in the diagram as shown)**

Turns out you can. Only ports marked as aggregate on the Fortiswitches are considered MCLAGs.
MCLAGs are automatically set up when you turn them on explicitly.

**Can the ICL be a double-cable as in my picture? The Fortinet page shows the ICL as a single cable.**

Yes it can. The switches will recognize that.

**What steps exactly are required to achieve this configuration?**

1. Set up the Fortigate with ports 1,2 as an Aggregate FortiLink, and enable fortilink-split-interface on the aggregate.
2. Connect the two core switches with all their cables. They will all appear on the Fortigate and will set up a trunk with each other automatically. The two cables between the two Fortiswitches will be seen correctly.
3. From the CLI of the Fortigate, do exec ssh admin@<FSW-IP> to log in directly to the first Fortiswitch. Then add ```set mclag-icl enable``` to the switch trunk configuration, like so:
    ```
     config switch trunk
        edit "D243Z14000288-0" // name derived from FortiSwitch-2 SN
            set mode lacp-active
            set auto-isl 1
            set mclag-icl enable
            set members "port21" "port22"             
        end
    ```
4. Do the same on the other Fortiswitch
5. At this point the Fortiswitches will set up the ICL for MCLAGs. The Fortigate should recognize that the two Fortiswitches are MCLAG peers and will set up the uplinks between Fortigates and Fortiswitches as MCLAG.
6. You can then turn off fortilink-split-interface on the Fortigate aggregate interface.

That’s it!
