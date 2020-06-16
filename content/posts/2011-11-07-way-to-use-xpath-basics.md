---
layout: post
title: Way to use XPATH-Basics
--- 



 {{page.title}}
======================================================




Hello Service-now Diary,

Its very difficult for me to understand this very difficult recursive function for processing XML given in this location
<a link="http://wiki.service-now.com/index.php?title=XMLDocument_Script_Object#XMLHelper">CommunitySnippet</a>

All that I want to do is I have a XML document and I want to process it.

Regards,
Hiranya

Dear Hiranya,
That example is a very good way to process complex XML, But then it may <em>seem</em> tough,especially if you are in hurry! ;)
TO cover the basic stuff to access a Node:
<strong>xmldoc.getNodeText("<Identifythenode>")</strong>-This method is used to get the Value of the node,whose node path is given in the parameters.
<strong>xmldoc.getNode</strong> is used to get a pointer to the desired node.
<strong>xmldoc.getNodes("path-of-a-series-of-nodes*");</strong> 
Now you would like to know how to get the path of the name. For this I strongly recommend to have a look at the XPATH tutorial in w3cschools.
<a link= "http://www.w3schools.com/xpath/">XPATH-W3C</a>
P.S When you go through the examples in W3C,You will find that processing is done for firefox and IE separately(Also it is on the client-side) but on Service now you need not worry about this.

Now the entire process of processing the XML comes down to simple two steps,
1.Identifying the node(nodes) you want to use the value
2.Using anyof the method to get the value of the node.


Also,You can store the Node pointers in arrays,by using the getNodes() method( ,and process it using a while or for loop.
For example,
Supposing this is the Structure of XML You want to process is something like this
<pre lang="xml">
<root>
<child>
<grandchild>
<text>something</text>
</grandchild>
<grandchild>
<text>something</text>
</grandchild>
<grandchild>
<text>something</text>
</grandchild>
<grandchild>
<text>something</text>
</grandchild>
</child>
<child>
<grandchild>
<text>something</text>
</grandchild>
<grandchild>
<text>something</text>
</grandchild>
<grandchild>
<text>something</text>
</grandchild>
<grandchild>
<text>something</text>
</grandchild>
</child>
</root>
[/code]

The code will be something like this
[code][/code]
var i=1;
var arr = [];
var check = xmldoc.getNodeText("//child[1]");
while(check != null){
for(var j=1;j<=5;j++){
var a =xmldoc.getNodeText("//child["+i+"]/grandchild["+j+"]/*");
arr[j]= a;
}
i++;
var check = xmldoc.getNodeText("//child[i]");
}
</pre>

This is a basic code. You can count the nodes as well and use them in the loops.But I feel this ways clean,the oldschool's way with less functions and more code ;-)

Hope this helped hiranya.

Cheers,
Service-Now Diary


 




-