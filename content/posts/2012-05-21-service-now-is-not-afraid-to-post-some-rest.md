---
layout: post
title: Service Now is not afraid to POST some REST
--- 



 {{page.title}}
======================================================




This is a supplement to this post <a href= "http://www.john-james-andersen.com/blog/service-now/service-now-is-not-afraid-to-get-rest.html">Service Now is not afraid to get some Rest</a>

I recently came across a  tool that can only understand REST. In this post we will be Sending a POST call over HTTP using Service Now's java Packages.
You can see another method of setting parameters at this <a href= "http://community.service-now.com/forum/4902"> URL</a> on community. But in this blog post we will be POSTing an XML


You can test it out in Scripts- Background
<pre lang="javascript">
var xml  = Packages.java.lang.String();
gs.log('Started'+current.number);
</pre>
/**
Creating the XML
**/
<pre lang="xml">

xml = "<?xml version=\"1.0\" encoding=\"UTF-8\"?> "+
					"<action>"+
					      "<type>provisoning</type>"+
					      "<program>Abhiram</program>"+
					      "<name>Mysql database needed for Devlopment Env</name>"+
					      "<description>Mysql database needed for Devlopment Env</description> "+     
					     " <details>"+
					      	    "<attribute>"+
					      	    	"<id>cloudProvider</id>"+
					      	    	"<value>private</value>"+
					      	   " </attribute>"+
					      
						    "<attribute>"+
							"<id>serviceOffering</id>"+
							"<value>small</value>"+
						   "</attribute>"+
					
					
					      	    "<attribute>"+
					      	    	"<id>quantity</id>"+
					      	    	"<value>1</value>"+
					      	    "</attribute>"+
					      	  
					
					      	   " <attribute>"+
					      	    	"<id>platform</id>"+
					      	    	"<value>linux</value>"+
					      	    "</attribute>"+
					
					
					      	    "<attribute>"+
					      	    	"<id>os</id>"+
					      	    	"<value>Redhat</value>"+
					      	   " </attribute>"+
					
					
					      	    "<attribute>"+
					      	    	"<id>osVersion</id>"+
					      	    	"<value>Redhat6.2</value>"+
					      	    "</attribute>"+
					
					
					      	    "<attribute>"+
					      	    	"<id>softwareversion</id>"+
					      	    	"<value>mysql-5.1.47-4.el6</value>"+
					      	    "</attribute>"+
                                                    
      						     "<attribute>"+
					      	    	"<id>taskid</id>"+
					      	    	"<value>"+current.number+"</value>"+
					      	    "</attribute>"+
                                                    
								
								
								
					      "</details>"+
					"</action>";
</pre>


<pre lang="javascript">

/**
Setting the String Request Entity
**/
				
var sre = new Packages.org.apache.commons.httpclient.methods.StringRequestEntity(xml);
var client = new Packages.org.apache.commons.httpclient.HttpClient();
var post = new Packages.org.apache.commons.httpclient.methods.PostMethod("http://321.566.677.155:8080/Prov/action/doAction.xml");
gs.log("XML"+xml);


post.setRequestEntity(sre); 
            post.setRequestHeader("Content-type","application/xml"); 
                 
            var result = client.executeMethod(post);
            gs.log("Response status code: " + result);         
            gs.log(post.getResponseBodyAsString());
gs.log('end'); 
            
</pre>
