---
layout: post
title: Serving a static WSDL in ServiceNow
--- 



 {{page.title}}
======================================================




Imagine that you are replacing a legacy tool with ServiceNow, and imagine this legacy tool talks to other legacy tools. To qualify this even more, imagine that the legacy tools can _only_ talk to ServiceNow via Webservices, and these Webservices SHOULD support the exact same format (and the same number of functions) as the webservices of the legacy tool that ServiceNow is replacing.


<img src ="https://upload.wikimedia.org/wikipedia/commons/0/04/Pound_layer_cake.jpg"/>
You'd probably tell me to write different Scripted Webservices, to mimic the function and give the new WSDLs to the legacy tools. But the legacy tools would come back and tell you, they CANNOT accept different WSDLs, all they can accept is the exact same WSDL. What do we do now?

We serve a [Static WSDL](http://wiki.servicenow.com/index.php?title=Creating_a_Static_WSDL).

At this point I want to you go and configure the Static WSDL following the instructions on the WIKI. I'll wait.

Now, on a 10,000ft view how the static WSDL concept would work is, 

- You would create a Static WSDL and you copy the WSDL of the legacy tool's ( the one ServiceNow is replacing) Webservice in there.

- Next, you create a new Scripted Webservice with the same name, and leave the script blank.
- Finally, you'd call a Script Include from the Scripted Webservice, and pass the request object, do a bunch of stuff and return the response back.


Notice that, as per the instructions on the WIKI, you are supposed to change the SOAP endpoint of the WSDL with the Scripted Webservice's SOAP endpoint. You have to do this because, when you consume the WSDL and do any HTTP Post from the SOAP Client, you will **always** hit the SOAP endpoint of the Scripted Webservice you created, and the Scripted Webservice will process your request and return back a meaningful response.


I'm planning to post a library to make transactions of this kind easier very soon.