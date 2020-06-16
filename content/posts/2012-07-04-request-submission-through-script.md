---
layout: post
title: Request Submission through Script
--- 



 {{page.title}}
======================================================





<pre language ="javascript">
// nuke the current cart
nukeCart();
// new cart
var realCart = getCart();
var cartID = realCart.sys_id;
// order a blackberry
addToCart(cartID, "05ef1c960a0a3cdd00587525899ea18f", 1);
addOptions(cartID);
doOrder();
function addToCart(cartid, cat_item, quantity) {
   var gr = new GlideRecord('sc_cart_item');
   gr.initialize();
   gr.cart = cartid;
   gr.cat_item = cat_item;
   gr.quantity = quantity;
   gr.insert();
}
function addOptions(cartID) {
   // Get the Cart Item
   var kids = new GlideRecord('sc_cart_item');
   kids.addQuery('cart', cartID);
   kids.query();
   if (kids.next()) {
      // Look up the options for the item in the cart
      var options = new GlideRecord('item_option_new');
      options.addQuery('cat_item', kids.cat_item);
      options.query();
      // Add the appropriate Item Options
      while(options.next()) {
         var gr = new GlideRecord('sc_item_option');
         gr.initialize();

/* These are the variables to be included for the request, for example Brief description of the request is
the "Brief Description of Request */
         if(options.question_text == 'Brief description of the request') {
            gr.item_option_new.setValue(options.sys_id);
            gr.value = current.u_mlt_bpe_comments;
         }
      }
}

function nukeCart() {
   var cart = getCart();
   var id = cart.sys_id;
   var kids = new GlideRecord('sc_cart_item');
   kids.addQuery('cart', cart.sys_id);
   kids.deleteMultiple();
}
function getCart() {
   var cart = new GlideRecord('sc_cart');
   var userid = gs.getUserID();
   cart.addQuery('user', userid);
   cart.query();
   if (cart.next()) {
      // we already have a cart all is well
   }
   else {
      cart.initialize();
      cart.user = userid;
      cart.insert();
   }
   return cart;
}
function doOrder() {
   var req = new Packages.com.glideapp.servicecatalog.RequestNew();
   req.copyCart();
   gs.addInfoMessage('Item created');
}


</pre>

Courtesy : Mohammed Ishaq Khan