---
layout: post
title: JSON Encoding the Meta Data in ServiceNow
--- 



 {{page.title}}
======================================================




We will be walking through:

- How to JSON encode columns of a table.


##### How to JSON encode the meta data

By meta-data I mean the columns of a table. If you were like me, you would be thinking why can't we just do a `Glide` to `sys_dictionary` and get all the data. Sure. You could do that, but if you are really concerned about the performance, I suggest you look another epic-beautiful Script Include called `DBHelper`. If you read through it, it makes you feel that you will be able to write any JS Pattern in ServiceNow Script Include.

**Bonus**

If you use `DBHelper` you don't have to take the pains of storing the other key attributes of Dictionary like `label`,`length` etc., you get everything from the JSON object that the function of `getColumnInfo` returns.

Here is an example invocation

	var cinfo = DBHelper.getColumnInfo('sc_req_item'); //ciinfo is JSON. 
    JSUtil.logObject(cinfo);
    

*DBHelper is no longer available in Eureka. I have no idea why. I can still see it in my instance.

Here is the Script of DBHelper:

<script src="https://gist.github.com/abhididdigi/154569491cb446638e33.js"></script>



    