---
layout: post
title: Dynamic Rule Base Generation - Order Guide
--- 



 {{page.title}}
======================================================




A brand new theme. Really liked it. If are wondering why there is an underscore before the blog name, I would say its more because I always liked putting an underscore before defining any variables.It makes me feel more geeky.

This post will explain on how you can generate a dynamic rule base for an Order Guide of a Service Catalog.Suppose you have four options given to you in an Order Guide form, A,B,C and D. You will have to map them to their respective Catalog Items in the Rule base so that when you click on "Choose Options" you get a form with all these 4 items(supposing you have checked all the 4 options). Below, I will explain you a way to automate the rule base,using a onSubmit script, and a Script Include.This process will check if the Rule corresponding to an option checked is already present in the rule-base,and if it is not present will add one. Mind you this happens for every onSubmit of the order guide page.

Introduction: The rule base for an order guide is stored in a table called, sc_cat_item_guide_items. The condition is stored in variable : condition and the variable that you are mapping in the rule-base is stored in the variable named variable.

I had the freedom of writing the sys_ids of the options selected because, i myself created the options using my own UI page. The details of what  the UI page is for another day. My onsubmit will write the SYS_ID's of all the options selected to a Single line text(Comma separated). Now from my onSubmit will call this script include which will take the sys_ids and insert corresponding records into Rule-base table.
You can extend this as well to send the Variable name you are mapping in the Rule-base if you are having many order guides and planning to have a dynamic rule-base.

Implementation :
Onsubmit Client Script on Order Guide Page.This will look more or less like this,
<pre lang="javascript">
function onSubmit() {
   var arr = document.getElementsByClassName("options");
/*As I mentioned earlier in the post, 
this is a UI Page variable that I created.
So gave the class as option there*/
   var i=0;
   var str = new Array();
   while(arr[i]){
      if(arr[i].checked == true){
        str.push(arr[i].name);
      }
      i++;
   }
   g_form.setValue('slt_mapping_Components',str);
   var ga = new GlideAjax('OrderAI');
   ga.addParam('sysparm_name','ruleAI');
   ga.addParam('sysparm_sys_ids',g_form.getValue('slt_mapping_Components'));
   ga.getXMLWait();
   ga.getAnswer();

  }</pre>
This is a synchronous call to Glide Ajax. I know, You might be wondering why am I using synchronous call than asynchronous. The reason is,if you use an asynchronous call, if the rule base is not present, then the onSubmit script runs(and completes) before the record gets inserted into rule base table. Hence the Synchronous call.

Script Include :

Name :OrderAI
[code lang="javascript"]
var OrderAI = Class.create();
OrderAI.prototype = Object.extendsObject(AbstractAjaxProcessor,
{
   ruleAI: function()
   {
      var list=  this.getParameter('sysparm_sys_ids').split(',');
      
      var check=0;
      for(var i=0; i&lt;list.length;i++){
         var gr = new GlideRecord('sc_cat_item_guide_items');
         gr.addQuery('condition','CONTAINS','IO:464317d0ff902000ae69fb56e77efe8d');
         /**********************************************************************************************
          IO:464317d0ff902000ae69fb56e77efe8d is the variable name,You can get this value either from the
         rule base table, or you can write a client script on this variable and copy the Variable column 
         in the list view of a Client Script. You can as well pass this value to the script include from 
         the on Submit *********************************************************************************/
         gr.query();
         
         while (gr.next()) {
            var con = gr.condition.toString();
            if(con.indexOf(list[i]) != -1){
               check=1;
            }
            
         }
         if(check == 0){
            this._ruleAI(list[i]);
         }
      }      },
      _ruleAI: function(sysID) {
         var crb = new GlideRecord(&quot;sc_cat_item_guide_items&quot;);
         crb.initialize();
         crb.item = sysID;
         var condition = &quot;IO:464317d0ff902000ae69fb56e77efe8dLIKE&quot;+sysID+&quot;^EQ&quot;;
         crb.condition = condition;
         crb.guide = &quot;873c4ed8ff502000ae69fb56e77efe5e&quot;;
         crb.insert();
         gs.addInfoMessage(&quot;New Rule Base added.&quot;);
      }});


[/code]


Note: This might be a performance killer because of the onSubmit heavy lifting done.

As you can see i had hardcoded the values in the script include, you can as well pass the values from the onSubmit script.

<strong><em>Acknowledgments</em></strong></a> :
Loyola Ignatius- For the Idea of the Dynamic Order Guide Creation.

