---
comments: true
date: 2010-05-19 19:48:47
layout: post
slug: testing-windows-workflow-activities-in-isolation
title: Testing Windows Workflow activities in isolation
wordpress_id: 26
---

There comes a time in every CRM developer's career when he's tired of doing the rain dance of plugin re-registration for testing custom workflow activities. Of course, moving all of the heavy lifting out of the actual Execute() method is a must, but sometimes we just need to test the thing as-is.

I've seen [some references](http://msdn.microsoft.com/en-us/magazine/dd179724.aspx) to testing workflow activities on the web that recommend using the workflow runtime to spin things up for the test, but for smoke testing individual activities, I think that this is probably overkill. What I'm going to do here is to spin up an instance of the activity outside of the WF runtime environment. For another take on this as well as a sample of spinning up the actual WF runtime for testing, check out [this reference](http://odetocode.com/blogs/scott/archive/2006/08/02/unit-testing-workflow-activities.aspx). Of course, if we have a composite activity or if we reach into the execution context at all, this technique won't work. If need be I might look at mocking the execution context, but for now I don't need it.

As an example, take the following custom CRM workflow activity that sends a notification to a web service with a contract ID that has been updated in CRM

[sourcecode language="csharp"]
public class ContractUpdateActivity : Activity
{
    public static DependencyProperty ContractNumberProperty =
        DependencyProperty.Register(
            "ContractNumber",
            typeof( string ),
            typeof(ContractUpdateActivity )
        );

    [CrmInput("ContractNumber")]
    public string ContractNumber {
        get {
            return ( string )base.GetValue( ContractNumberProperty );
        }
        set {
            base.SetValue( ContractNumberProperty, value );
        }
    }

    protected override ActivityExecutionStatus Execute( ActivityExecutionContext executionContext ) {
        Service service = new Service();
        service.ContractUpdated( this.ContractNumber );
        return ActivityExecutionStatus.Closed;
    }
}
[/sourcecode]

Even though this is far from being a unit test or free of side effects, we'd still like to run this activity as a smoke test outside of CRM. There are two potential problems with doing something like

[sourcecode language="csharp"]
ContractUpdateActivity updateActivity = new ContractUpdateActivity();
updateActivity.Execute( null );
[/sourcecode]

The most obvious issue, were one to attempt compilation of the above code, is that the Execute() method is protected and not visible to us in a test. Fortunately, Execute() is defined on System.Workflow.ComponentModel.Activity which intended for use as a base class and is not sealed.

So, instead of using ContractUpdateActivity directly in our test, we can make a small wrapper by subclassing it:

[sourcecode language="csharp"]
public class ContractUpdateActivityTest : ContractUpdateActivity {
    public void Execute() {
        base.Execute( null );
    }
}
[/sourcecode]

now we can write a few lines of code to exercise the code (I hesitate to call this a 'test') via the subclass:

[sourcecode language="csharp"]
ContractUpdateActivityTest testUpdateActivity = new ContractUpdateActivityTest();
testUpdateActivity.ContractNumber = "12345";
testUpdateActivity.Execute();
[/sourcecode]

The other issue to watch for is that of dependencies. The type initializer of Activity tries to load dependencies behind the scenes and won't look in the current path of the executable that is running the test. The required classes must either be compiled into the same assembly as the workflow activity or be found in the GAC. It may be possible to locate the assemblies somewhere else, but I haven't looked at the fusion logs to find out where else workflow looks for assemblies.

If an assembly is not found, the .net runtime will issue an error similar to the following:

[sourcecode]
System.TypeLoadException: Could not load type 'Service' from assembly 'ContractServices, Version=4.0.0.0, Culture=neutral, PublicKeyToken=null'.
[/sourcecode]
