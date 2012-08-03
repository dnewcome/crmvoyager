---
comments: true
date: 2011-10-27 21:04:58
layout: post
slug: using-active-directory-domain-accounts-with-copssh
title: 'Using Active Directory domain accounts with CopSSH '
wordpress_id: 554
---

If you're like me, you have clients behind firewalls and special development environments configured to mirror the client's environment for local testing. In my case a lot of my development environments have Microsoft CRM installed on them as a [self-contained development environment](http://crmvoyager.wordpress.com/2011/02/12/create-a-fully-standalone-crm-2011-environment-on-amazon-ec2/) replete with Active Directory. This means that the virtual machine does not have any local accounts, only domain accounts. When I log in, I'm logging into a domain.

This works out pretty well most of the time. However, recently I wanted to use my [SSH backhaul trick](http://crmvoyager.wordpress.com/2010/06/29/connecting-to-ms-crm-over-ssh/) to grab some data from a client's site back through their firewall. In order to get this to work, I had to do some extra experimentation with [CopSSH ](http://www.itefix.no/i2/copssh)user accounts and my [VirtualBox](https://www.virtualbox.org/) settings.

First off, let me recap exactly what we are trying to do. It might be worthwhile to look at my [SSH backhaul article](http://crmvoyager.wordpress.com/2010/06/29/connecting-to-ms-crm-over-ssh/) first, but what we are doing is running a secure shell server locally on the virtual machine and connecting to it from the remote server using [Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/). This lets us access things like Microsoft CRM services on the remote machine for doing things like data dumps and schema upgrades. 

I'm using VirtualBox as my virtualization environment. I happen to be using NAT (network address translation) instead of bridged network connection. This means that there is one extra step that I didn't cover in my previous article, which I will outline here. The complete end-to-end scenario becomes:

Putty on remote server -> firewall on my local network -> VirtualBox NAT on my laptop -> VirtualBox VM -> CopSSH daemon

So I covered everything in the previous article except for setting up VirtualBox NAT. Fortunately it is very simple. We need to set up port forwarding across the NAT. To do this go into the network settings of the running virtual machine and look for the button that says "port forwarding". This lets you set up the host and guest port. I had to set the IP addresses rather than leave them blank, but what I did was set both to 0.0.0.0, which means "all addresses". Here is a screenshot:
[![](http://crmvoyager.files.wordpress.com/2011/10/nat.png)](http://crmvoyager.files.wordpress.com/2011/10/nat.png)
I'm mapping the SSH port 22 to port 2222 to avoid conflicts with the native sshd daemon that is running on my Ubuntu laptop.

Once we have this set up, we can test the connection locally by using Putty to connect to localhost on port 2222. We should get a login prompt from CopSSH.

Once we know that CopSSH is working and reachable via the port forwarded over the VirtualBox NAT, we need to authorize the user accounts that can log in via SSH. This is where we have to pay close attention. The thing that caused me a lot of pain was that the domain and account names are case sensitive. When adding the user account put the domain name in all capitals and pay attention to the case. Check out the screenshots:

[![](http://crmvoyager.files.wordpress.com/2011/10/copssh-user.png)](http://crmvoyager.files.wordpress.com/2011/10/copssh-user.png)

One last thing: the user accounts will need the right to log on locally. Double check using the Local Security Policy tool (look in Administrative Tools):

[![](http://crmvoyager.files.wordpress.com/2011/10/copssh-domain.png)](http://crmvoyager.files.wordpress.com/2011/10/copssh-domain.png)

Test logging in with the domain account using domain\user:

[![](http://crmvoyager.files.wordpress.com/2011/10/putty-login.png)](http://crmvoyager.files.wordpress.com/2011/10/putty-login.png)

If login works, set up the tunnel the same way as in my previous article and rock on!
