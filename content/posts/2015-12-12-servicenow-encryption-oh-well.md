---
layout: post
title: ServiceNow encryption, oh well...
--- 



 {{page.title}}
======================================================




I actually like the way ServiceNow encrypts. It's more dummy user friendly. You don't have to know a lot of concepts to encrypt decrypt stuff.

I currently work on a product that deals in some _really_ confidential information. For our next version of the product we are actually building a PII database that is hosted on the internal network to not store any PII information in ServiceNow.

Anyway - coming back .. the requirement is to encrypt an attachment that contains confidential information, and we get that attachment from an email. 

So, because we had a lot of teams involved, I did my part, set up a Script Include that takes in an attachmentID and encrypts the attachment for the given attachment ID. Next I wrote some unit cases, executed them in the Scripts Background and closed the ticket saying, everything is fixed. 

Next, I set up an inbound email action to read the attachment and pass the attachment sys_id to the Script Include that I created above so that the attachment will be encrypted. Everything is logical and should work right?

The nightmare started a day before UAT. The emails would come with an attachment, they would get stored into `sys_attachment` table, BUT they wouldn't be encrypted. 

After 6 hours of reading posts on community, along with endless episodes of How I met your mother, I finally found a small note in the Easy Encryption share app by Ben that said "You cannot encrypt a field or attachment directly from a Webservice/Email" but it doesn't mention as to why.

I thought about it, and I think this is the reason why:

When a user with an encryption context logs into ServiceNow, a new key value pair would be added to the current context. The key is set to `Encryption Context` and the value is the sys_id of the encryption context that the user is part of. You can retrieve the encryption context with the below invocation:

    var s = gs.getSession().getEncryptionContext();
    gs.log("The current context" + s.getCurrent());

interestingly enough.. when you have this piece of code in the inbound email action, you will NOT be getting any context. But if you UPDATE a record from the inbound email action AND write a business rule to run the same code, you will find the encryption context. This is also the case if you have a Script Action/Webservice etc., you will have to trigger a Business Rule to find the context of the user who is responsible.

Strange, ain't it? Oh well..



