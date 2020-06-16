---
layout: post
title: The PopupOpenStandard method & window.opener
--- 



 {{page.title}}
======================================================




This week I was working on a requirement  where in I need to

I.Have a slush bucket on a Catalog Item's form which will have a list of all Catalog Items(from the cat_item table), and clicking on any of the entries should take you to the catalog item form(which is a pop-up) and submitting it should create a  Cart item.

For this, I figured out that I thought of writing a Glide Window, But somehow, I wanted to do it more simpler, and more over I need a pop-up which is similar to Reference Selection pop-up,i.e., it should disappear if you click anywhere else.
I came up finally with this method- nothing written on my own, but a method from external javascript libraries called the PopOpenStandard.

Also, I had to override the custom DB click event of the Right Slush bucket, so that I can write my own code when someone double clicks, it should give a pop-up(the usual functionality is it moves the entry to left slush bucket)
The code is simple and it goes something like this :

<pre lang="javascript">
function onLoad() {
var fe= g_form.getControl('<name_of_your_slush_variable>_select_1');
fe.ondblclick = fun;
}
function fun(){
/* The URL in there, is used to open a catalog item's form. You can pass different parameters to
the UI page,com.glideapp.servicecatalog_cat_item_view and also visible to all the
macros called within it. */
popupOpenStandard("/com.glideapp.servicecatalog_cat_item_view.do?sysparm_view=&sysparm_id=9e107d7b0a0a3cdd0018276a5e3f79c1&sysparm_cart_edit=9d3fc027ff522000ae69fb56e77efe3c&sysparm_seq=yes");
}
</pre>

This is how you call any URL in the popOpenStandard method.
<hr/>

II. Now that we are giving an option to fill the Catalog Item's form, We need to copy the price from the pop-up(which is a catalog item form in itself) and the parent form that is calling the pop=-up. This I did using a Global Function in the UI script and calling it using the top.window.opener.<function_name> method.

I would post the code, might come handy to people working on pop-ups. As price is a complex thingy I would skip this for now, and explain how to call the function rendered on the Parent's form(the Global UI Script)

 Created a onSubmit() which runs only when the catalog_item is called as a pop-up( This again is a long story, I sent a parameter in the popupOpenStandard - sysparm_seq, which is again copied into a hidden variable on the UI macro catalog_item) and in the onSubmit call the Calling form's function :


function onSubmit() {
top.window.opener.funcGlobal(100);
}


The funcGLobal is a function defined in the UI script, with Global as true. Also remember, you can set any variable on the form in funcGlobal using g_form.setValue('variable_name','value);

Another Small tip, If you have to read the Price in any catalog item's client script for doing some calculation use the following script :

<pre lang="javascript">
function onSubmit() {
alert(gel('price_span').innerHTML);
}
</pre>
<a href="http://servicenowdiary.com/wp-content/uploads/2012/10/blog.png"><img src="http://servicenowdiary.com/wp-content/uploads/2012/07/blog-300x160.png" alt="" title="blog" width="300" height="160" class="alignright size-medium wp-image-319" /></a>

For the entire code of PopupOpenStandard use any DOM inspector like firebug.


Any suggestions/comments are welcome.






 