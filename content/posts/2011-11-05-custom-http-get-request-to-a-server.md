---
layout: post
title: Custom HTTP GET Request to a server
--- 



 {{page.title}}
======================================================




I need to send a custom Get Request from dynamic XML generator over HTTP protocol. The HTTP option in the Data Source wasn't working because there was no static file at the given location. It generates the XML on the fly !
So there was a need that I need to write my own custom HTTP Get Request.

Thanks to Loyola Ignatius,for identifying the authentication glitch and providing the necessary Auth functions and John Anderson for helping me out with parsing the response object.
Now coming to the GET Request Construction

<pre lang="javascript">

 Assumption: The link if its something like this
http://abhcde.servicenow.com/ADG/SendEntries.nsv/3E337078Cfh44560A85257904006E96EA?SendEntries

var httpclient = Packages.org.apache.commons.httpclient;
var HttpClient = httpclient.HttpClient;
var UsernamePasswordCredentials = httpclient.UsernamePasswordCredentials;
var GetMethod = httpclient.methods.GetMethod;
var client = new HttpClient();
var AuthScope = httpclient.auth.AuthScope;
var authScope = new AuthScope("abhcde.servicenow.com", 80, null);

var credentials = new UsernamePasswordCredentials("********", "********"); //username and Password
client.getState().setCredentials(authScope, credentials);
var get = new GetMethod( "http://abhcde.servicenow.com/ADG/SendEntries.nsv/3E337078Cfh44560A85257904006E96EA?SendEntries");
get.setDoAuthentication( true );
var status = client.executeMethod( get );
var result = get.getResponseBodyAsString();
gs.print(result);
// From here start the code for parsing the XML using XPath which i will write in another post of mine
}
</pre>

More on authscope
http://hc.apache.org/httpclient-3.x/apidocs/org/apache/commons/httpclient/auth/AuthScope.html
All the classes are JavaClasses so you will be getting the documentation at
http://hc.apache.org/httpclient-3.x/apidocs/org/apache/commons