---
layout: post
title: A Server side function to Percent Encode
--- 



 {{page.title}}
======================================================




If you aren't sure what is Percent Encode, Here is the <a href="http://tools.ietf.org/html/rfc3986#section-2.1">RFC</a> describing it.

If you aren't sure why you need Percent Encode, Here is the link to Twitter <a href="https://dev.twitter.com/docs/auth/percent-encoding-parameters">oAuth Percent Encode </a>page. We basically encode out parameters, when we send out Authorizations/Requests using oAuth protocol. I was working with a couple of such services, which only accept oAuth and desperately needed a Server Side solution. If you need it on the Client Side, there is a more elegant solution  - You can use <strong>encodeURI</strong> or <strong>encodeURIComponent.
</strong>Linking an <a href="http://stackoverflow.com/questions/332872/how-to-encode-a-url-in-javascript">excellent post</a> covering both these functions.

This post specifically speaks about the Server Side function of Percent Encode, for Service Now.
<div style="background: none repeat scroll 0 0 #FFFBFB; border: 2px solid #980000; margin: 20px 0 10px; padding: 0px 5px 5px 10px; position: relative; color: #000000;"><b>Note: </b> This post uses Package calls. After Calagry, Package calls are no longer supported. Use the Calgary <a href="http://wiki.servicenow.com/index.php?title=Managing_Packages_Call_Removal_Tool_Errors">Package Migration tool </a>to migrate your package calls.</div>
Without further ado, Here is the script:

[code lang="javascript"]

percentEncode:function(params){

	  if(typeof params =='string'){

         var ENCODING = &quot;UTF-8&quot;;
         var s = params;
         s= s.toString();
         var a = Packages.java.net.URLEncoder.encode(s, ENCODING)
         .replace(&quot;+&quot;, &quot;%20&quot;).replace(&quot;*&quot;, &quot;%2A&quot;)
         .replace(&quot;%7E&quot;, &quot;~&quot;);

         return a;

      }

      if(typeof params == 'object'){
         var arr = [];

         for(var i in params){
            if (params.hasOwnProperty(i)) {
               gs.log(&quot;percent encodes&quot;+ this.percentEncode(i)+&quot;=&quot;+this.percentEncode(params[i]));
			   arr.push(this.percentEncode(i)+&quot;=&quot;+this.percentEncode(params[i]));

            }

         }

          return arr.join('&amp;');

      }

   },

[/code]

Twitter is just an example of a service which uses Percent Encode. Percent Encode will(mostly) be used when ever you work with oAuth protocol, which Service Now is yet to introduce. On a side note, We can still mimic oAuth using HTTPClient's  GET and POST.

Let me know what you think in the comments.