---
layout: post
title: A simple Bar-Code generator with basic features.
--- 



 {{page.title}}
======================================================




<p>Today, let's take a look at how to create a Bar code label in Service Now. I've seen this being asked so manytimes, and recently here : http://community.servicenow.com/forum/8960 . This started me off, and here is it.</p><p>The end product will look like this :<a href="http://servicenowdiary.com/wp-content/uploads/2013/05/ServiceNow-Service-Automation-2013-05-05-08-38-43.png"><img class="aligncenter size-medium wp-image-615" alt="ServiceNow Service Automation 2013-05-05 08-38-43" src="http://servicenowdiary.com/wp-content/uploads/2013/05/ServiceNow-Service-Automation-2013-05-05-08-38-43-300x185.png" width="300" height="185" /></a></p><p>1. We will have a UI Page, where you can specify the table name, 2 column names( this will take the top two columns of the barcode ), and the column name you want to barcode-code(if you leave this blank, then it will automatically encode sys_id)</p><p>The barcode landing page will look something like this :<a href="http://servicenowdiary.com/wp-content/uploads/2013/05/landing-page.bmp"><img class="aligncenter size-medium wp-image-613" alt="landing page" src="http://servicenowdiary.com/wp-content/uploads/2013/05/landing-page.bmp" /></a></p><p>Note: I've not even touched a tiny bit of CSS to the above page. If you are interested in having a great look using CSS, just add style tags, or import a Style sheet into your UI page as discussed <a href="http://chrishann.net/posts/2012/12/3/servicenow-style-sheets-and-caching">here</a>.</p><p>2. We will have a Global button called "Print Barcode". This is a global button, and will appear in all those tables which have entry in a table called barcode_template table. The barcode_template table will store the preferences such as Column names that should appear, and the column name that needs to be encoded, along with the table name. A sample of data in barcode_template table:<a href="http://servicenowdiary.com/wp-content/uploads/2013/05/ServiceNow-Service-Automation-2013-05-05-08-35-30.png"><img class="aligncenter size-large wp-image-614" alt="ServiceNow Service Automation 2013-05-05 08-35-30" src="http://servicenowdiary.com/wp-content/uploads/2013/05/ServiceNow-Service-Automation-2013-05-05-08-35-30-1024x198.png" width="625" height="120" /></a></p><p>Now, lets go into how we do it:</p><p>All the magic happens with a jQuery plugin called BarCode Generator hosted here  <a href="http://barcode-coder.com/en/barcode-jquery-plugin-201.html">Barcode generator</a>. All I had to do was to get it into a UI Script and write a simple UI Page, to take the table name, and the columns and then encode it and display the results.Lets start by creating a simple landing UI page:</p>

<b>Name:</b> Barcode_landing

HTML:

[code language="xml"]

&lt;?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot; ?&gt;
&lt;j:jelly trim=&quot;false&quot; xmlns:j=&quot;jelly:core&quot; xmlns:g=&quot;glide&quot; xmlns:j2=&quot;null&quot; xmlns:g2=&quot;null&quot;&gt;

&lt;div style=&quot;margin:auto&quot;&gt;

TableName: &lt;input type=&quot;text&quot; name=&quot;tname&quot; id=&quot;tname&quot;/&gt;&lt;br/&gt;

&lt;label&gt;You can specify 2 columns, comma separated, and they will be displayed in the first two columns of Barcode Label.&lt;/label&gt;&lt;br/&gt;
ColumnName(s): &lt;input type=&quot;text&quot; name=&quot;cname&quot; id=&quot;cname&quot;/&gt; &lt;br/&gt;

&lt;label&gt; Column name to be barcoded &lt;/label&gt;&lt;br/&gt;
Field to be Barcoded: &lt;input type=&quot;text&quot; name=&quot;fname&quot; id=&quot;fname&quot;/&gt; &lt;br/&gt;
Encoded Query: &lt;input type=&quot;text&quot; name=&quot;enc&quot; id=&quot;enc&quot;/&gt; &lt;br/&gt;

&lt;input type=&quot;button&quot; onclick= &quot;fnSubmit()&quot; name=&quot;GetLabels&quot; value =&quot;GetLabels&quot;&gt;&lt;/input&gt;


&lt;/div&gt;
&lt;/j:jelly&gt;

[/code]

Client Script:

[code language="javascript"]
function fnSubmit(){
    
    window.location = &quot;/barcode_generator.do?tname=&quot;+$('tname').value+&quot;&amp;cname=&quot;+$('cname').value+&quot;&amp;fname=&quot;+$('fname').value+&quot;&amp;enc=&quot;+$('enc').value+&quot;&amp;stub=no&quot;;
    
    
    
}
[/code]




<p>Here in the above UI page, we take all the inputs from the user, and send it to another UI Page. Notice the parameter, <em>stub</em> which is <em>no</em>, if we are doing it from this UI page - in bulk , or is it from the UI action on a particular table, for which the value will be <em>yes</em>. tname corresponds to Table Name,cname corresponds to comma separated column names, that take the two columns of the Label, fname corresponds to the field to be encoded and enc corresponds to encoded query.If you leave it blank, all the rows from the table tname, will be Barcode labeled.


 Now, let's talk about the UI page that generates the Barcode: 
<b>Name :</b> Barcode_Generator 
<b>HTML</b></p>



[code language="xml"]

&lt;!--?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot; ?--&gt;

//Get all the parameters from the URL using the RP object.
//Check if you are coming from a UI page, or from a UI action
//Yes = from UI action, No = from UI page(bulk)
stub = RP.getParameterValue('stub');
//Get the table name.
tname = RP.getParameterValue('tname');

//If it is from the UI page, then you already have all the information specified in the UI page.
if(stub == 'no'){
//Get the comma separated column names, and store it in an array.
cname = RP.getParameterValue('cname').split(',');
//Get the field name to be barcode encoded into fname.
fname = RP.getParameterValue('fname');

if(fname == ''){
//If nothing is specified, by default take the sys_id and encode it.
fname = 'sys_id';
}
}

//If coming from the UI action on the table(single record barcode generate)
else if(stub == 'yes'){
//Glide the barcode template table, and get the column name, and field name from the table record for that //table
var grTab = new GlideRecord(&quot;barcode_template&quot;);
grTab.addQuery('tablename',tname);
grTab.query();

if(grTab.next()){
cname = grTab.column.split(',');
fname = grTab.fname;

}
}

//Finally get the encoded query - 
enc = RP.getParameterValue('enc');

gr = new GlideRecord(tname);
gr.addEncodedQuery(enc);
//Prepare the gr object
gr.query();

&lt;/g:evaluate&gt;

&lt;head&gt;
&lt;!-- Include jQuery --&gt;
&lt;script type='text/javascript' src='jQuery.min.jsdbx'&gt;&lt;/script&gt;

&lt;!-- Include Barcode Generator,Link below. --&gt;
&lt;script type='text/javascript' src='barcode.min.jsdbx'&gt;&lt;/script&gt;

&lt;!-- take care of some CSS styles. --&gt;
&lt;style&gt;
p.thicker {font-weight:900;}
table,td
{
border:1px dotted #98bf21;
text-align:center;
font-weight:1900px;
font-size: 120%;

},


&lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div style=&quot;margin-left:5cm&quot;&gt;
&lt;table width=&quot;732&quot; &gt;

&lt;!-- The rest is pure jelly xD --&gt;
&lt;j:while test=&quot;${gr.next()}&quot;&gt;
  &lt;tr&gt;
    &lt;td width=&quot;359&quot; height=&quot;64&quot;&gt;&lt;p class=&quot;thicker&quot;&gt;${gr.getValue(cname[0])}&lt;/p&gt;&lt;/td&gt;
    &lt;td width=&quot;357&quot;&gt;&lt;p class=&quot;thicker&quot;&gt;${gr.getValue(cname[1])}&lt;/p&gt;&lt;/td&gt;
  &lt;/tr&gt;
  


  &lt;tr &gt;
    &lt;td colspan=&quot;2&quot; width=&quot;732&quot; height=&quot;78&quot; align=&quot;center&quot;&gt;

&lt;!-- This div is a placeholder for the Barcode. We recognize it with the class, and encode the id.Check the Client Script.--&gt;
&lt;div style=&quot;margin:auto;&quot; id=&quot;${gr.getValue(fname)}&quot; class=&quot;bcode&quot;&gt; &lt;/div&gt;


&lt;/td&gt;
  &lt;/tr&gt;
&lt;tr style=&quot;border:none;background-color:#EAF2F3;&quot;&gt;
&lt;td  colspan=&quot;2&quot; style=&quot;border:none;background-color:#EAF2F3;&quot;&gt;
$[sp]
&lt;/td&gt;

&lt;/tr&gt;
 
&lt;/j:while&gt;

&lt;/table&gt;
&lt;/div&gt;
&lt;/body&gt;

&lt;/j:jelly&gt;
[/code]

Now, here is the place where we insert the Barcode - 

<b> Client Script </b>

[code language="javascript"]
(function($){
  
	$('.bcode').each(function(){ $(this).barcode($(this).attr(&quot;id&quot;), &quot;code128&quot;);     })
    
})($j||jQuery);

[/code]

<b> -- This ends the bulk Bar code Generation -- </b>

Let's now talk about generating it being on a record of a table. For that I've created a UI action like this:

Name: Print Barcode
Table: Global
client: checked
onClick : showBarCode()
Condition: new BarCode().isThere(current.getTableName())


[code language="javascript"]

function showBarCode(){
   var url =' /barcode_generator.do?        tname='+g_form.getTableName()+'&amp;enc=sys_id='+g_form.getUniqueValue()+'&amp;stub=yes'; 
    popupOpenStandard(url);
    
}

[/code]

OKay, now we are done, except for a Script Include by the name <em>BarCode</em> that we use to validate if we have to show that button on a particular table. Here is the Script Include:

[code language="javascript"]

var BarCode = Class.create();
BarCode.prototype = {
    initialize: function() {
    },
    
    isThere:function(tName){
        var gr = new GlideRecord(&quot;barcode_template&quot;);
        gr.addQuery('tablename',tName);
        gr.query();
        if(gr.next()){
            return true;
        }
        return false;
        
    },
    
    
    type: 'BarCode'
}

[/code]

Easy ain't it? Couple of things to wrap this post up:

Firstly - You can download the barcode jQuery plugin from <a href="http://barcode-coder.com/en/barcode-jquery-plugin-201.html"> Barcode Generator </a>

Secondly - As i have told many times, I'm not a very good CSS designer.So I only worked with basics here. Feel free to improve upon the CSS.

Three - If you have noticed the UI page, you will know any layout is possible. So mix and match HTML and comeup with the layout that you need.

Most important: To push a jQuery plugin into a Service Now UI Script, you sometimes need to tweak it a tiny little bit, as it conflicts with Prototype. I'll soon write a post on it, but until then you can use the UI Script here : <a href="https://docs.google.com/file/d/0B7F5ETTqhZmEZVdHTHZCd0t1SW8/edit?usp=sharing"> Barcode UI Script </a> and use it in your instance.

Here is the update set : <a href="https://docs.google.com/file/d/0B7F5ETTqhZmESWlvdTV2ZGs0YlE/edit?usp=sharing"> Barcode Generator - Service Now Update set </a>

Let me know what you think in the comments!



