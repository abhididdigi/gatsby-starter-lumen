---
layout: post
title: Create a Service Request from Change and associate it
--- 



 {{page.title}}
======================================================




I've not searched for any other way to do this, but when i saw this <a href="http://community.servicenow.com/forum/14487">community post</a> here wanted to do it myself and this way:

Here we ,

1. Have a button on the change_request form, which when you click on it will open a Slushbucket which will have all the Catalog Items in it.
2. The user,when he selects the Catalog Item from left to right slushbucket, and clicks on the catalog item in the right slush bucket, a new tab will open in which he will have an option to fill the Catalog Item's form he selected.
3. When he opens a Catalog Item form, the Change Request from where he is opening this Catalog Item's form, will be populated in a variable(present in a variable set) called change_request.
4. Now finally, we write a Business Rule to populate back the request created into corresponding Change Request ticket in a field called "Associated Request".

Create a new field on Change_Request by the name Associate Request, which references to Request(sc_request) Table.


Creation of the Create a Service Request button on Change Request form :

Type : UI Action
Name : Create a Service Request
Client : checked
onClick : addCatItem
[javascript gutter="true"]
function addCatItem(){
   //Open a dialog window to request items that we want to add.
   var dialog = new GlideDialogWindow('add_service_request');
   dialog.setTitle('Select Request Item');
   dialog.setPreference('sysparm_groupQuery', 'active=true');
   dialog.render();

   return false;
}[/javascript]
Type: UI Page
Name : add_service_request
[xml]
&lt;!--?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot; ?--&gt;
&lt;table border=&quot;0&quot;&gt;
&lt;tbody&gt;
&lt;tr&gt;
&lt;td&gt;Please select the Service Request which you want to order.&lt;/td&gt;
&lt;/tr&gt;
&lt;tr&gt;
&lt;td&gt;&lt;!-- Include the 'ui_slushbucket' UI macro --&gt;&lt;/td&gt;
&lt;td align=&quot;right&quot;&gt;&lt;!-- Include the 'dialog_buttons_ok_cancel' UI macro --&gt;&lt;/td&gt;
&lt;/tr&gt;
&lt;/tbody&gt;
&lt;/table&gt;

[/xml]
Client Script:
[javascript]
addLoadEvent(function(){
   //Load the groups when the form loads
    document.getElementById('slush_right').ondblclick = dbClick;

   slush.clear();
   var ajax = new GlideAjax('GroupCatalogItems');
   ajax.addParam('sysparm_name', 'getItems'); 
   ajax.getXML(loadResponse);
   return false; 
});
function dbClick(){
var sysID  = gel('sys_uniqueValue').value;
url =  '/com.glideapp.servicecatalog_cat_item_view.do?sysparm_id='+this.value+'&amp;amp;sysparm_chg='+sysID;
window.open(url);

}
function loadResponse(response){
   //Process the return XML document and add groups to the left select
   var xml = response.responseXML;
   var e = xml.documentElement; 

   var items = xml.getElementsByTagName(&quot;item&quot;);
   if(items.length == 0)
      return;

   //Loop through item elements and add each item to left slushbucket
   for (var i = 0; i &amp;lt; items.length; i++) {
      var item = items[i];
      slush.addLeftChoice(item.getAttribute('value'), item.getAttribute('text'));
   }
}[/javascript]
Type: Script Include

Name : GroupCatalogItems
[javascript]
var GroupCatalogItems = Class.create();

GroupCatalogItems.prototype = Object.extendsObject(AbstractAjaxProcessor, {

   getItems : function() {  
      var gr = new GlideRecord(&quot;sc_cat_item&quot;);
      gr.addQuery('active', true);  
      gr.query(); 

      //Add the available groups to select from
      while (gr.next()) {
         var item = this.newItem();
         item.setAttribute('value', gr.getValue('sys_id'));
         item.setAttribute('text', gr.getValue('name'));
}
}
      });[/javascript]
Creation of a Variable Set - Creation of a Variable - Creation of a Catalog Client Script on Variable Editor :
[gallery]

Catalog Client Script :

[javascript]
function onLoad() {
var test = getUrlVars();
g_form.setValue('change_request',test['sysparm_chg']);
}

function getUrlVars() {
   var vars = [],
   hash;
   var hashes = window.location.href.slice(window.location.href.indexOf('?') + 1).split('&amp;');
   
   for (var i = 0; i &lt; hashes.length; i++) {
      hash = hashes[i].split('=');
      vars.push(hash[0]);
      vars[hash[0]] = hash[1];
   }
   
   return vars;
}
[/javascript]

Please note, the important thing is to Add this Variable set to which all Catalog Items that you need to order from a Change Request.

Finally, to map, the change_request with the request, you need to write this Business Rule.
Name : Assosiated Request
Table : sc_req_item
condition : !current.variables.change_request.nil()
After : true
When : Update
[javascript]
gs.addInfoMessage('Its getting inserted');
var gr  = new GlideRecord('change_request');
gr.get(current.variables.change_request);
if(gr){
if(gr.u_assosiated_request == ''){
gr.u_assosiated_request  = current.request;
gr.update();
}
}
[/javascript]

You should have the change_request in the requested item variable change_request .. you can access it using current.variables.change_request. And the above business rule will populate it in the Change Request table by populating the field Assosiated Request.
