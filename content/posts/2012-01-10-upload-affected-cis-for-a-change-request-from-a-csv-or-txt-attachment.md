---
layout: post
title: Upload Affected CI's for a Change Request from a .csv or .txt attachment
--- 



 {{page.title}}
======================================================




Happy new year ! This post will explain the functionality to  Upload  the list of  Affected CI's from an .csv or .txt attachment for a change request. Thanks to <a href="www.servicenowguru.com">SNC Guru</a> for providing me with a couple of ideas on handling Attachments in various ways.

Assumption : The text/csv will have one column where we have the names of affected CI's for a particular change request.

There are 3 parts to get this working,
1.Identify the attachment from the sys_attachment table corresponding to a change request.
2.Converting the attachments to a byte array,converting the byte to array of strings.
3.Inserting the data into task_ci table.

When I had this requirement at the start, I had very less information to work on.Just a class called
SysAttachment and a method called getBytes(Inherited from the Object class). 

I am handling the entire conversion of the entire attachment into an array of strings in the UI action itself. You can use a Script Include and call this Script Include from the UI action if you choose to make this client callable.

Name of the UI Action : AddCI's
//This will only get the CI names from a csv sheet. You can do similar thing for a .txt file.
<pre lang="javascript">
var gr = new GlideRecord('sys_attachment');
gr.addQuery('table_sys_id', current.sys_id);
gr.addQuery('file_name','UploadCI.csv');
gr.addQuery('content_type','application/vnd.ms-excel');
gr.query();
if (gr.next()){
   var sa = new Packages.com.glide.ui.SysAttachment();
   var binData = sa.getBytes(gr);
   var string = new Packages.java.lang.String(binData);
   gs.log(string);var arr = string.split("\n");
   for(i=0; i<arr.length ; i++){
     var taskCI = new GlideRecord("task_ci");
     taskCI.initialize();
     taskCI.task = current.sys_id;
     var str = arr[i].toString();
     var len = str.length();
     var newStr = str.substring(0,len-2);
     var grA = new GlideRecord("cmdb_ci");
     grA.addQuery("name",'STARTSWITH',newStr);
     grA.query();
     if(grA.next()){
       taskCI.ci_item = grA.sys_id;
       taskCI.insert();
     }
     else {
       gs.addInfoMessage(" There is no CI by the name:"+arr[i]);
     }
   }
   action.setRedirectURL(current);
}
else{
   gs.addInfoMessage("You haven't attached any file by the name UploadCI.csv");
   action.setRedirectURL(current);
}
</pre>








