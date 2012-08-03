---
comments: true
date: 2011-02-12 23:57:22
layout: post
slug: create-a-fully-standalone-crm-2011-environment-on-amazon-ec2
title: Create a fully standalone Microsoft CRM 2011 environment on Amazon EC2
wordpress_id: 403
---

I set up a development environment that I can share in the cloud with my colleagues and partners for sharing ideas and quick demos. There are already a few articles out there that can help you get the latest CRM 2011 bits set up on a virtual machine, but I didn't find anything specific to EC2 or anything on installing everything on one server including Active Directory (so the whole setup is completely self-contained), so I'm writing up my experiences here for you. Keep in mind that we are installing a release candidate here, as the final bits aren't available yet.

Briefly, the steps are:





  * Create Windows Server 2008 EC2 instance


  * Promote instance to Active Directory domain controller


  * Install IIS+ASP.NET


  * Install Sql Server 2008 R2


  * Install CRM 2011



For this guide I'm assuming that you have met a few prerequisites:


  * An Amazon AWS account


  * An MSDN subscription or retail copy of SQL Server 2008 R2


  * The CRM 2011 RC [bits](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=c3f82c6f-c123-4e80-b9b2-ee422a16b91d)



Also, I won't belabor every individual installation step. I'll cover the high points necessary to get things installed so that CRM 2011 won't have any installation issues. This means I'll be accepting the default installation details for SQL Server. If you need to customize things feel free to do so, but I haven't tested other configurations so it may not work as expected.

There is a great guide [here](http://crmscape.blogspot.com/2010/09/creating-ms-crm-2011-vm-part-1-of-2.html) for installing the CRM 2011 Beta on a local virtual machine. He mentions that it will take a full day to set up, but using Amazon's available Windows images, we should be able to get that time down to about 3 hours. If you already have an EC2 image set up with a password set and have the SQL Server installation media you might be able to do this in just over two hours.



## Start the EC2 instance



Signing up for an Amazon Web Services account is a prerequisite for all of this. I won't go into it here, but there is now a nice [management console](https://console.aws.amazon.com) for AWS, so starting up EC2 instances is now pretty easy using the web. The critical piece is getting the right server image the first time. We want the following three things:





  * An EBS based image


  * Windows Server 2008 64bit


  * Without SQL Server pre-installed


[![](http://crmvoyager.files.wordpress.com/2011/02/ami.png)](http://crmvoyager.files.wordpress.com/2011/02/ami.png)
It is important not to use a SQL server image, as you will pay more per hour and it won't have Reporting Services installed anyway. 64bit is important because the CRM release candidate is 64bit only. EBS is important if you want to stop the server later without rebuilding everything. The alternative to EBS is AMI, which is backed by S3. In order to keep your changes (i.e. all of the stuff we are going to be installing) with an AMI instance will require you to "bundle" the instance every time new changes are made that you want to keep. I recommend EBS since it is much simpler and only slightly more expensive. Detailed instructions on launching a Windows instance and decrypting the admin password can be found [here](http://www.michaelckennedy.net/blog/2010/01/31/BuildingWindowsMachinesInAmazonEC2.aspx). I won't go into it here since it is beyond the scope of this article.

There is one critical configuration step to take before we proceed. By default, Windows EC2 instances have dynamic hostnames which are generated when the instance boots up. This will cause problems later on once we have promoted the box to the domain controller role and have SQL server installed. Forgetting this step is not fatal, but changing the host name on a domain controller can be a pain, so best to avoid having to do it altogether.

Amazon achieves the dynamic naming using a Windows service that is installed on the base image. We can configure the behavior of this service using a tool called ec2 service properties, which can be found in the start menu. All we need to do is uncheck the setting for "Set Computer Name" per the following screenshot:

[![](http://crmvoyager.files.wordpress.com/2011/02/ec2-setname.png)](http://crmvoyager.files.wordpress.com/2011/02/ec2-setname.png)



## Promote server to Active Directory domain controller



The first thing that I do (after changing the admin password - we'll be rebooting a few times) is promote the server to an Active Directory domain controller. CRM requires a domain, and since we are going for a self-contained installation, we have to promote the CRM server itself to the domain controller role. Ordinarily it is a bad idea for a domain controller to have any other server role, but for a development environment this will be fine. Additionally, promoting the server before we install SQL helps avoid additional configuration steps as a result of the server's name and user accounts changing.

Promoting a domain controller is done from the commandline by running

[sourcecode]
c:\>dcpromo.exe 
[/sourcecode]

The promotion takes a while, so I grab the SQL Server install media from MSDN during this time. Transfer rates from MS are pretty fast and it should download by the time the DC is ready to go.

[![](http://crmvoyager.files.wordpress.com/2011/02/newdomain.png)](http://crmvoyager.files.wordpress.com/2011/02/newdomain.png)

[![](http://crmvoyager.files.wordpress.com/2011/02/domain-name.png)](http://crmvoyager.files.wordpress.com/2011/02/domain-name.png)

We'll also need to install an ISO mounting program to install SQL Server. I use MagicDisc, which can be found [here](http://www.magiciso.com/tutorials/miso-magicdisc-overview.htm). Get the 64 bit Vista version for Server 2008. In the case that you are using Server 2008 R2, get the Windows 7 version.

You'll have to reboot after dcpromo has done its thing. Remember to use the new domain name when you log back into the server. The login will require domain\user now that the server is in a domain (it _is_ the domain!).



## Install SQL Server 2008 R2


Once the SQL installation media is ready, install MagicDisc and mount the ISO. Run the setup and select a new SQL installation. I used the defaults for everything, installing all services. The most important thing to get installed beyond SQL Server itself is SSRS, so at the very least make sure that Reporting Services is checked off. 

Since we have promoted the server to domain controller status, the default local accounts cannot be used as the SQL Server service accounts. Create a new non-admin user called 'sqlserver' and use that as the service account. 

[![](http://crmvoyager.files.wordpress.com/2011/02/sqlserver-account.png)](http://crmvoyager.files.wordpress.com/2011/02/sqlserver-account.png)

[![](http://crmvoyager.files.wordpress.com/2011/02/sqlserver-user.png)](http://crmvoyager.files.wordpress.com/2011/02/sqlserver-user.png)


Also note that the SQL Browser service needs to be manually enabled and started. This will be necessary later on in order for the CRM installer to find the SQL server instance.

[![](http://crmvoyager.files.wordpress.com/2011/02/sql-default-install.png)](http://crmvoyager.files.wordpress.com/2011/02/sql-default-install.png)



## Install IIS and ASP.NET



Installing IIS is accomplished by adding the Web server role to the server using the server management console. Just note that in addition to the IIS role we'll need ASP.NET, which must be selected separately. Once these two things are selected, the installer will install them both simultaneously.

[![](http://crmvoyager.files.wordpress.com/2011/02/webserver-role.png)](http://crmvoyager.files.wordpress.com/2011/02/webserver-role.png)

[![](http://crmvoyager.files.wordpress.com/2011/02/asp-net.png)](http://crmvoyager.files.wordpress.com/2011/02/asp-net.png)



## Install CRM 2011 Release Candidate



Now for the moment we have been waiting for. Run the CRM 2011 RC installer. If the .NET Framework 4.0 is not installed, the CRM installer will install it and ask you to reboot, which is a minor annoyance, but the installer will stop and wait for you to do it, so don't let it go unattended for too long or you'll never make it in under three hours! I used the defaults everywhere possible and set the service accounts to "Network Service". You'll have to browse for the SQL Server instance but the installer should find the SRSS server automatically.

[![](http://crmvoyager.files.wordpress.com/2011/02/orgname.png)](http://crmvoyager.files.wordpress.com/2011/02/orgname.png)

[![](http://crmvoyager.files.wordpress.com/2011/02/service-accounts.png)](http://crmvoyager.files.wordpress.com/2011/02/service-accounts.png)

I had a lot of missteps along the way to getting all of this installed, but on the last run I sailed through it in under 3 hours including getting the 4GB SQL Server installation media from MSDN. Hopefully you have similar results and you won't have to spend a whole day setting things up.
