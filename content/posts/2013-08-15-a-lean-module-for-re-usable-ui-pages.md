---
layout: post
title: A Lean Module for Re-usable UI Pages.
--- 



 {{page.title}}
======================================================




Idea:

Many a time, we use a lot of UI Pages to get information from the user on Tasks/Requested items, which need to be stored again in a related list. Everytime its a pain to start and code them from the scratch using all the DOM Manipulations.

I'm talking about those UI pages, where You have a list of columns, and when the user clicks on "Add row" a new row appears, where in he fills the information, and when everthing is filled in, click on "Save and Close". I don't know about you, but our Project uses this a lot. So making it more re-usable, as it will be easy to configure pages like these.


How we do it:

1. Have a Table where the options can be confiured as a JSON string.
2. The variables can be(as of now) a text box, index, reference, and datetime.
3. If it is a reference, then you will have to specitfy the name of the table you are working with.As of now, no support for ref-qual, as it gets messy with all setting the ID and all. So write your own before query business rules to filter the rows.
4. The UI Page gets the information from this table, and correspondingly fetches, the information about all kinds of fields and uses them.
5. The Admin can also specify how many of these variables does he need. The order is again from the order variable defined in the JSON string.
6. There is also the faility to store this information in a table and the m2m column on that table.

The sameple JSON string will be like this:

[code] {&quot;input&quot;:[{&quot;order&quot;:&quot;100&quot;,&quot;count&quot;:&quot;3&quot;,&quot;columnName&quot;:&quot;caller_id&quot;}],&quot;reference&quot;:[{&quot;order&quot;:&quot;200&quot;,&quot;count&quot;:&quot;1&quot;,&quot;tableRef&quot;:&quot;incident&quot;,&quot;coulmnName&quot;:&quot;incident&quot;}]} [/code]

This JSON will be retrieved and they will be accordingly and the DOM created.





 