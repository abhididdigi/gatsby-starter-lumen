---
layout: post
title: Wunderlist - ServiceNow API
--- 



 {{page.title}}
======================================================




OK, so finally Wunderlist released a Public API. You can read about the API here: https://developer.wunderlist.com/documentation. The API like all the other APIs use the oAuth 2.0, but Wunderlist isn't exactly 2.0. It is a partial implementation of oAuth 2.0.

If you scan Github for any libraries for Wunderlist, you will not really find any that refer to the new API ( there are a couple, but they use unofficial APIs), So I thought I could write one because I had a brand new Pebble time AND I was at Bozeman, chilling out and tubing and a lot of time to spend.

The reason why I chose ServiceNow is because I was speaking to one of the Integration Architects and he was asking me what my gotchas are about the new Scoped Application API. I never gave the API a spin, so this looked like an excellent use case to test the Scoped Application API. There is a lot of stuff that I learnt by writing this API, but there is really a lot to do in the app because of the limitations of the Scoped Application API. 

So here is what the flow would look like, we only go through some snippets of code to the final product but not the final code in itself.

Below the Highlevel Architectural flow and the components I use to mimic oAuth. When I first saw that ServiceNow supports oAuth, I was excited that I can authenticate other systems using oAuth, then I understood that ServiceNow supports oAuth for authenticating other systems to pull the data, and ServiceNow doesn't itself provide an API to communicate with other systems, so I had to write an entire landing UI page and a combination of a processor to do so. 

High level steps:
- A user wants to get her tasks from Wunderlist into ServiceNow.
- She will first need to get something called an Access Token to authenticate my App to talk to Wunderlist, so that my app can use this Access Token to authenticate everytime the user wants to retrieve her applications.
- But before an Application can get hold of an Access Token, it has to first make a call to Wunderlist, and let the the user verify the Application which will make Wunderlist give back something called `code` to the Application invoking it.
- Once you have a `code` with you, you will have to exchange `code` for `AccessToken` and store the `AccessToken` in a table and linked to a user, so that we don't have to do all the above steps when ever we try to fetch the tasks/lists for the user.


A more technical look at what is happening here:

- User wants to use the API. She wants to retrieve tasks and lists. 
- The user should first have to authenticate ServiceNow. So we have a UI Page called " Authenticate User" to do it, where in we set all the parameters that Wunderlist expects ( like the redirection API etc., read more about it here : https://developer.wunderlist.com/documentation/concepts/authorization)
- Next, if the user is okay with your URL then Wunderlist will return something called `code` to an endpoint hat you specify in the above step. For this, I used a Processor, which will take any request that it gets and invokes a Script Include to parse the request and get the `code`.
- The same Script Include also makes a final call to get an access control by passing in the client ID and client secret ( which you are given on Wunderlist APP manage page for you application). This way we exchange the `code` to Access Token.
- Now we have the Access Token, we store it in a table against the user who just authorized the request.
- After we have Access Token, we then make more serious calls.


Let's knock couple of things off before we continue:

###### Good things with Scoped Glide API:


- An excellent documentation for `g_request` and `g_response` objects and even a documentation page. [documentation page.](https://developer.servicenow.com/app.do#!/api_doc?to=class__glideservletrequest)
- Excellent support for REST API calls. You no longer have to create a REST message before you access something. More dynamic setting of the REST body, headers and params.
- Excellent support for sync and async REST calls.
A snippet of code, that demonstrates (2). This is from a Script Include that does all the stuff.

```

_makeRequestFromRestMessage:function(code){
		var code = code+'';
		var client_id = gs.getProperty("x_4591_wunderlist.wu_client_id");
		var client_secret = gs.getProperty("x_4591_wunderlist.wu_client_secret");
		try{
			var r = new sn_ws.RESTMessageV2('wu_accessToken', 'post');
			r.setStringParameter("client_id", client_id);
			r.setStringParameter("code", code);
			r.setStringParameter("client_secret", client_secret);
			r.setHttpTimeout(10000) //In milliseconds. Wait at most 10 seconds for response from http request.
			response = r.execute();
			responseBody = response.haveError() ? response.getErrorMessage() : response.getBody();
			status = response.getStatusCode();
			this._output(responseBody, status);
			return responseBody;
		}catch(e){
			gs.info("error in the API call");
		}
	},


	_makeRequestDirect:function(code){

		var code = code+'';
		var client_id = gs.getProperty("x_4591_wunderlist.wu_client_id");
		var client_secret = gs.getProperty("x_4591_wunderlist.wu_client_secret");
		var body  = {"client_id":client_id,"client_secret":client_secret,"code":code};
		body = new global.JSON().encode(body);
		try{
			var restMessage = new sn_ws.RESTMessageV2();
			restMessage.setHttpMethod("post");
			restMessage.setRequestHeader("Content-Type", "application/json");
			restMessage.setEndpoint("https://www.wunderlist.com/oauth/access_token");
			restMessage.setRequestBody(body);
			global.JSUtil.logObject(restMessage.getRequestHeaders(), "The headers");
			global.JSUtil.logObject(restMessage.getRequestBody(), "the body");
			var response = restMessage.execute();
			responseBody = response.haveError() ? response.getErrorMessage() : response.getBody();
			status = response.getStatusCode();
			this._output("`call is successful - `"+responseBody, status);
			return responseBody;
		}catch(e){
			gs.info("error in the API call");
		}


	},
	
```

- An excellent log and error handling mechanism. You now even have a separate log table for all the applications.
- And finally a very nice scoping mechanism. For you to invoke any Script Include, you will need to use it's Scope. For example if you would want to invoke JSUtil, then you would actually use `global.JSUtil.logObject` and remember when you do so, this would get logged into "Global" log table because you are using a global object.


###### What's bad:

- No Scoping for UI Page. You **cannot** invoke a scoped Script Include from a UI page. Even if you use `application.<scriptinclude>`. You __SHOULD__ have the Script Include defined in the `global` scope for you to even access it from a UI Page.
- My biggest pain in the rear is the limitation of `g_request`. If you are using `g_request` in a Application Scope, then you don't have access to any of the functions except the ones here : [link](https://developer.servicenow.com/app.do#!/api_doc?to=class__glideservletrequest). That means you dont have access to any of the inputStreams or Response Body and that means you cannot write your application if you want to handle a `post`. and that's why my Application is incomplete. ServiceNow didn't provide me with a way to access the request body yet for Scoped application, so if you want to access the body you will STILL have to write your applications in Global Scope.

More to come on this.

A sneak peak of my half baked API: https://thawing-fjord-6173.herokuapp.com/ [ built using a simple Node JS Server which access my API on ServiceNow ]

<img src = "http://i3.cpcache.com/product/203708327/made_in_bozeman_tile_coaster.jpg?height=225&width=225"/>
Code and Coffee are better in Mountains.


 







