---
layout: post
title: Why Decrease of Max_length of any Dictionary  will not be captured
--- 



 {{page.title}}
======================================================




Some times-Rarely though you would want to decrease the value of the max_length attribute of any Dictionary Entry.
For example : There is a Column called response in <pre language="javascript"> sys_soap_message_test </pre> which I by mistake increased its length.
Now, the Web Service doesn't run. I want to get the value of this max-length to its original value(to decrease it) Which is not possible. If you haven't tried it, Try it.

You can't decrease the value of max_length of any Dictionary Variable.A business rule will not allow this.

Name : <pre lang="javascript"> Dictionary change rationally </pre>

Description of the Business rule : 
<pre lang="javascript">
 This rule prevents decreasing the max_length of a field. If it is deactivated,
 and the length of an out-of-box field is decreased, the change will
 automatically be reverted in the next upgrade. This behavior is by design.
</pre>

If you want to decrease the length, then Deactivate this Business Rule and then, modify the length :)
(or)
If it is not so urgent, wait for the next upgrade it will be reset-ed to its original :)


