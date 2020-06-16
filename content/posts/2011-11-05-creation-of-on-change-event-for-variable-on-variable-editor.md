---
layout: post
title: Creation of 'On Change' event for variable on Variable Editor
--- 



 {{page.title}}
======================================================




Requirement :  You want to modify the value of any variable on the Variable Editor and You  need to hide/show corresponding variables,dependent on this variable 

I had gone through the entire community posts,and many other blogs,but I could not get any pointers. Finally the great DOM came to my rescue ;)

Here is the way how i achieved onChange on any variable that is on variable Editor.
This is just a draft of my idea. This solution may not to optimal :D,I am working on making it better.
http://wiki.service-now.com/index.php?title=Client_Scripts#Adding_another_event_Handler
Here on this page we have an explanation on how we can add an event to a page.But the problem with Variable editor is the ID of the variables on the variable editor changes from one task record to another making it difficult to get the ID of the element.
I have written an onLoad script, that will attach an onChange event.

[code]
function onLoad() {
var x=document.getElementsByTagName(&quot;select&quot;);//Get all the select boxes,as my requirement was related to a select box,so I got all those elemts.
elementId = x[2].name;//This code will get the ID of  third select box on the page. I need to improve on this line of code

var control = g_form.getControl(elementId);//gets the control of the element
control.onchange = myOnChange;
}

function myOnChange() {

var a = document.getElementById(elementId);
var b= a.value;
alert(b);
alert('Oh.. Did I just change? Yes I did,and my new value is this?! '+ b);
}
[/code]

This code is pretty simple. Not sure why it wasn't there anywhere :)

Any suggestions are welcome !