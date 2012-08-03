---
comments: true
date: 2010-06-29 15:42:44
layout: post
slug: connecting-to-ms-crm-over-ssh
title: Connecting to MS CRM over SSH
wordpress_id: 57
---

How many times have you been in the situation where, for whatever reason, you are stuck developing a CRM application against a customer's test CRM installation where you don't have a proper VPN setup so that you can develop locally? How about having to use 2 or more remote desktop sessions to even reach the box in question? Throw a Citrix connection into the mix and you have a sure recipe for pain. Not to mention that installing Visual Studio with all of its dependencies, along with the sheer size of it, is no walk in the park either.

I had a situation recently where the customer was on CRM 3.0, making it nearly impossible for us to set up a local development server with their schema on it. The customer's test environment was on a relatively slow internet link, and the only access that we had to the box was over a Citrix connection to a bastion host and then on to the target machine using RDP.

Fortunately, there is a solution to our dilemma, and we don't even have to install anything on the server! [Secure shell](http://en.wikipedia.org/wiki/Secure_Shell) (SSH) is the traditional UNIX remote connection utility which has long had the ability to forward network connections from one machine to another along with its traditional command console duties. Unfortunately, since the server was a Windows machine, no SSH server was installed by default, and installation of a third-party SSH implementation would have been too intrusive.

However, SSH has the ability to do both local and remote tunneling, so we can effectively reverse the roles of the client and server in order to create a backhaul link over which we can access CRM. This lets us install the SSH server on our client machine where we want to do the development work (eg, run Visual Studio) and use a simple client ([Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/)) on the server to initiate the tunnel.

I installed the [CopSSH](http://www.itefix.no/i2/copssh) SSH server on my laptop and opened port 22 on my firewall to let the remote connection through from the outside. I won't cover installation of CopSSH here, but it is well documented and installation went smoothly for me on Windows Vista. Once CopSSH is set up, the only tricky part left is to configure Putty correctly to create a forwarded port over which we can access CRM services.

Putty doesn't need to be installed, just run putty.exe on the server and give it the IP address of your local development machine. My configuration looks like the following:
[![](http://crmvoyager.files.wordpress.com/2010/06/putty-crm.png)](http://crmvoyager.files.wordpress.com/2010/06/putty-crm.png)

I chose port 9982 as the port I want to use on my laptop to access CRM. On the CRM server end, I used 'localhost:80' as the destination, because the CRM server is running on the actual box that I'm connecting from (localhost) on the standard HTTP port (80).

Now I just have to change my CRM URLs in development to something like:
[sourcecode]
http://localhost:9982/mscrmservices/2006/CrmService.asmx
[/sourcecode]

Why do we use 'localhost' here if we are connecting to the server? Remember that the SSH tunnel is now listening on the laptop, so when we connect to this port that SSH is listening on, it takes the connection and forwards it on to the server. So, to our .NET code it looks like CRM is actually running locally on the laptop.

As an added bonus, URLs like the following will also be available locally:
[sourcecode]
http://localhost:9982/sdk/list.aspx
[/sourcecode]

This can dramatically reduce your development cycle time when the environment is not ideal. Hope this saves you some time.
