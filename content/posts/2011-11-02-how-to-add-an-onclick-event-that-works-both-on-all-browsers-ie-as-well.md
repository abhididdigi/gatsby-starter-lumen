---
layout: post
title: How to add an onclick event that works both on all browsers
--- 



 {{page.title}}
======================================================




Requirement :  You want to populate all the options on the Service Request Form dynamically from a table.So You had written your own UI page which will get all the options from a table.
One of the problems in writing our own UI pages with all the "fields" we want is we have to attach their event handlers and handle each and every validation :(

In the process of validating them, we had a statement,

<pre lang="javascript">

var element1 = document.createElement("input");

element1.type = 'checkbox'; // Defining the type of the element

element1.id = 'checkbox'+i //Just assigning some ID to the element created.

element.setAttribute("onclick",'handlerfunction(this)');

[/code]

It was working fine with Firefox, But when i tested it with IE, it wasn't working :(. One more reason for me to hate IE :|


Reason : setAttribute doesn't work in IE.

I explored all the links that google gave me with little success.Finally, I thought of using the "observe"  method of prototype.

<img src = "http://upload.wikimedia.org/wikipedia/en/b/b2/PROTOTYPE.png"/>

<a href= "http://www.prototypejs.org/api/event/observe">Prototype-Observe;Just a click away</a> 

You can find everything about events in the above link. Also one of the reasons I like about prototype when it comes to event handling is, You need not pass 'this' again to a handler function. 
the code is as simple as this
[code lang="js"][/code]
Event.observe(element1.id,'click',check); 

/*Observe will "observe" if there are any events. If there is an event, then it will check if there any handler function 
for that event. What we are doing here is registering onclick event with the check function.*/

</pre>
The list of the events that can be attached is given in this link :
<a href="http://www.w3schools.com/jsref/dom_obj_event.asp"> EventList </a> 
Prototype is one of the few libraries that are extremely useful ! Write less, Do more :) 