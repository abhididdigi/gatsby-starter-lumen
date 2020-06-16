---
layout: post
title: An Ajax call from a Chrome Extension to SNOW!
--- 



 {{page.title}}
======================================================




<p>I was going through the SNOW community and I stumbled upon this Simon Morris&#8217;s Chrome add-on, &#8220;The Totally un-official chrome add-on for SNOW&#8221;, I really liked the idea, wanted to do more with it.</p>

<p>The idea is use the current interface but get more features like,</p>
<ul><li>The Top Incidents that a person had raised,along with a summary of it when clicked on it in a window.</li>
<li>Along with the incidents the other must-have(planned) are the list of all the problems, the list of all the Service Requests, and their summaries.</li>
</ul><p>When I meant top its more the most recent ones or the recent updated ones. I have tested integrating the Add On with some Scripted Web Services of Service now, and yes it seems to be working.</p>
<p>The only problem here seems to be me getting accustomed to using the UI in the way dictated by Google Chrome.</p>
<p>I know, I had a FF extension coming but yea, its sill under construction. Once i am good with this chrome extension, Probably in the next couple weeks,I will translate with FF.</p>
<p>For making this thing work i used the Ajax Jquery call, which worked like a beauty.</p>
<p>Snippet here(of the call)&#160;:</p>
<pre class='"prettyprint'>  soapMessage += '';*/
	 soapMessage += '';
     soapMessage += '  ';
      soapMessage += '<a>' + 'helloworld' + '</a>';
     soapMessage += '';
     localStorage["last_record"] = 'incident';
     alert(soapMessage);
     $.ajax({
       url: "https://demo08.service-now.com/FirstCheck.do?SOAP",
       type: "POST",
       dataType: "xml",
       username: localStorage["username"],
       password: localStorage["password"],
       data: soapMessage,
       complete: createRecordCallback,
       contentType: "text/xml; charset=\"utf-8\""
</pre>
<p>Wierd enough, I wansn&#8217;t able to execute the same successfully on a normal HTML. Glad if some one would help.</p>