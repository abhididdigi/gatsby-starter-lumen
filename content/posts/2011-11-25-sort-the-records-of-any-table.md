---
layout: post
title: Sort the records of any table
--- 



 {{page.title}}
======================================================




Many a time, we want to sort the records of a table for display,supposing the user want to see the rows of a table sorted according to a particular field.

There are a couple of ways to do it.One of such ways is to use the Before Query business rule. I stumbled across these "before query" business rule just a couple of days ago.

A before query business is one which runsjust before the database is queried.
More information on <strong>BeforeQuery Business rules</strong> can be found in these links

<a href="http://www.servicenowguru.com/scripting/business-rules-scripting/controlling-record-access-before-query-business-rules/">Controlling Record Access before Query BRule</a>
<a href="http://www.servicenowguru.com/scripting/business-rules-scripting/fixing-before-query-business-rules-flaw/">
Fixing beforeQuery Businessruleflaw</a>

But I perceive(its not an accurate perception,but rather helps) it more like this :
If I have a before-query business rule, the code that you write in this BRULE will be running just "after" the records are rendered.
Supposing you write a sort, then the records will be sorted just after the records are fetched from the database.

Now coming to the solution of sorting the records,I guess you have already figured it out :)
You need to write a before query business rule on the table


[css classname="syntax-class" collapse="false" light="true"]
current.orderBy(&quot;&lt;columnName&gt;&quot;)
[/css]

I imagine there are couple of ways to write it like writing a refqual or changing the view(which I haven't tested) but once I do, I will post it too :)