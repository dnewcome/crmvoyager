---
layout: post
title: "Dropping a SQL database with NAnt"
date: 2013-02-06 19:01
comments: true
categories: 
---

I always like to automate my build processes, and one often-overlooked piece is automating database creation.

These days I use MSBuild a lot more, but I have plenty of legacy stuff that is still using NAnt exclusively. Database scripting is always
one of the big chores to take care of in any app, so there are likely lots of little scripts to run to get the database migrated
to the desired state. Most DML (data manipulation language) statements (insert/select/update) can be parameterized easily but 
DML statements like dropping a database are often tough to work with in the general sense.

The NAnt snippet below uses the sqlcmd commandline tool to run an inline script that drops the database given in the database.name 
property. The property is expanded by NAnt as text and becomes part of the script. In order to make this work correctly we have to get
creative with quoting. Notice the use of entity reference &apos; to insert the single quote characters needed for the database
name in the inline script.

Another thing to notice is that we put the database into single-user mode right before we drop the database. I used to take
the database offline to do this, but if we do it that way, the data files on disk won't get deleted during the drop
operation. Then when we go to create the new database it will fail since the files already exist.

I'm not sure if we could specify "replace" when creating the new database, perhaps that would be another solution.


```
<exec 
	program="sqlcmd.exe"
	commandline='-S ${database.instance} -b -d master -Q 
		"if exists( select name from sys.databases where name = &apos;${database.name}&apos; )
		begin
			print N&apos;${database.name} already exists on target instance, entering single-user mode&apos;
			
			alter database ${database.name}
			set single_user
			with rollback immediate;
			
			print N&apos;${database.name} already exists on target instance, dropping database&apos;
			drop database ${database.name};
		end"'
/>
```

This will allow a database to be dropped for later recreation. Enjoy!
