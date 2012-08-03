---
comments: true
date: 2010-07-12 06:50:57
layout: post
slug: getting-a-remote-shell-with-windows
title: Getting a remote shell with Windows
wordpress_id: 107
---

Have you ever wanted to do a quick IISReset on a remote server without having to switch to or start a remote desktop session? Shouldn't it be as easy as it is on Linux where you can have a console open for any machine that is running SSH (which is just about every Linux box)?

There are several different ways to get remote commandline access to a Windows machine. The guys at Sysinternals have offered a free tool called [PSExec](http://technet.microsoft.com/en-us/sysinternals/bb897553.aspx) for years now that works great if you are on a local network. However, it relies on a lot of tricks that include the Windows file sharing ports which are typically blocked if the machine is behind a firewall. Microsoft, starting with Windows Server 2008, provides its own next-generation shell called PowerShell that is supposed to make all of this pain go away. My experience is that there are so many steps needed to get two machines talking remotely over PowerShell (I'll blog about this sometime, as it can be worth it in some cases -- PowerShell really is quite, er, powerful).

Recently I wrote [an article](http://crmvoyager.wordpress.com/2010/06/29/connecting-to-ms-crm-over-ssh/) about creating backhaul SSH tunnels from a remote host back into your development machine. Today, I'm going to show you how you can use a tunnel set up using SSH to get a Windows command prompt on the remote server.

We are going to use a tool called Netcat to accomplish our goal. Netcat for Windows can be downloaded [here](http://joncraton.org/files/nc111nt.zip). We will need this tool on both ends of the connection -- client and server. Netcat needs no installation, but it is handy if we have it in the path somewhere so it can be invoked from the commandline easily. 

Once we have Netcat on both machines we can set up the server to listen on a port of our choosing and serve up cmd.exe over the network. On the client end, we will use Netcat again, but in client mode, to connect to the server and display the output from cmd.exe on the local machine.

On the server we invoke Netcat like so:
[sourcecode]
> nc -l -p 7777 -e "cmd.exe"
[/sourcecode]
Here we are telling Netcat to enter listen mode on port 7777, executing 'cmd.exe' when a client connects. Netcat will forward all input from the network to cmd.exe and return all output back over the network.

On the client end, we connect 
[sourcecode]
> nc localhost 7777
Microsoft Windows [Version 6.0.6001]
Copyright (c) 2006 Microsoft Corporation.  All rights reserved.
>
[/sourcecode]
Wait a minute, why are we connecting to 'localhost' if we really want to get a remote shell? Well in the [previous article](http://crmvoyager.wordpress.com/2010/06/29/connecting-to-ms-crm-over-ssh/), where we set up the SSH tunnel, we configured our backhaul connection to listen on the remote port 7777, and the client is that remote host, so from the client's perspective we need to connect to localhost. Then the tunnel does its thing, which is to forward traffic from the local port 7777 to the remote port, where we have Netcat listening.

We can prove that we are actually on the remote host by doing something like this:
[sourcecode]
> hostname
remote-srvr
>
[/sourcecode]

Hopefully this saves you some time, since now you can just switch to your command window and type 'iisreset' instead of messing with remote desktop. One thing that I'd like to get working, but haven't yet, is some console text editor. I have tried VIM and some others, but they all try to access the local console via some mechanism other than standard out, so they don't work. VIM tries to change the display mode of the console window that the remote Netcat process is running under rather than the local console window that the client is running in. Perhaps if we ran the Netcat server as a service without an attached console things would work. Hopefully someone can help me out in the comments.

One parting note -- the listening Netcat has no security, so anyone on the remote network can connect to it. As long as the remote network is secure and behind the firewall, using Netcat for remote connections shouldn't pose much of a risk, especially since once you are connected no one else can connect to your running Netcat process.
