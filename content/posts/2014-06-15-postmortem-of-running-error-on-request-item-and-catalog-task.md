---
layout: post
title: Postmortem of "Running Error" on Request Item and Catalog Task
--- 



 {{page.title}}
======================================================




---

###Prelude

Our ServiceNow instance had a very strange issue. 

>When someone opens a RequestedItem or Task, they see "running error", and it takes atlease 180 seconds for the form to open.

We didn't take a notice of this issue until after it was more than an issue. We had to get a workaround in  place in  few days.

------

###The Question

... and then I woke up, in a far different world than this one, trying to go on with life a day at a time, zero lines of code each day. We started to look into this, and after exhausting everything we could do, including Archival we filed a ServiceNow support ticket.

----


###The Setting

ServiceNow consultants started troubleshooting the issue on Webex as our ServiceNow is inhouse. There were two things they were interested in:

1. Check for the Database Query time that you get on each Semaphore listed on `stats.do?` page.

She immediately told us the issue was because of UIPolicies, she told me it was because of `UIPolicyBuilder` ScriptInclude. She asked me if we modified this ScriptInclude, so that it would be skipped on Upgrades. We did no such thing, so there was much she could do to help us resolve the issue.

.. we were short of time, so this was escalated to Application Support team soon.

The ApplicationSupport team came in and they gave some very important things that I didn't know about troubleshooting performance.

The first and logical thing they did was to use create a System Property called `glide.ui.template.trace`. And when you set this property to `true` and click on *Debug SQL*, you will start seeing the different SQL transaction times for all the queries that run *before* the RequestItem or CatalogTask page loads.

Soon, we realized it was ofcourse, the *UIPolicyBuilder*. We were seeing crazy transaction times for the SQL query that renders UIPolicies on the form.

---

### The Act

And, now we know what's the problem. The UIPolicy builder was the one that's causing the issue. But why now? The answer to this question lies in another question on what's the difference between a ClientScript getting rendered to a UIPolicy getting rendered.

When you write a UIPolicy, the code that you write along with FieldActions will be first parsed using a UIPolicyBuilder that will read the entire UIPolicy including the code and then render it on to the form. It *doesn't* do a straight up injection into the page. Take a note of this point, we will come back to this later.

At the same time, the code that you write in a ClientScript will be directly injected into the SourceCode of the a page. This is the major difference between a rendering of UIPolicy and ClienScript.

**How did this cause the Running error?**

When the UIPolicy builder is running on either RequestedItems or CatalogTasks, it is trying to render all the code that's present in the UIPolicy ( mind you, we have **lot** of code in UIPolicies, sometimes spanning across 1000 lines and more, this is because, ol'timers like us, had to write UIPolicies to control the behaviour of VariableEditor variables. But now, you can just check "run on requested item" and "run on catalog tasks" and get around with controlling the behavior of Variables on variable editor ). There is a unique part of the UIpolicybuilder that is a Regexp class, that tries to parse the entire UIpolicy code, and render it back on to the page, and this Regexp was taking lot of time to parse the code. This was the root cause for this issue. So what looked like a stright up running error had these concepts hidden behind it.

Because of the complexity of the entire UIPolicy code, we couldn't just scrap the entire code, so the only workaround we put in place was - to inject the code that's present in the UIPolicy directly on to the browser page, so that UIpolicyBuilder didn't have much to do. So we created UIScripts with the exact same code, that was there on UIPolicy to run on Requested items, and Catalog Tasks, and the running error disappeared.

That in a nutshell is how we got around the issue.
