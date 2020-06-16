---
layout: post
title: A Hack To Update Encrypted Fields
---

{{page.title}} 
===============

At the time of writing this post, ServiceNow still handles Encryption in a really not-so-good way. I'll explain, read on. 

Say you created a new Encrypted field. For the sake of brewity let's say you want to store the SSN information in ServiceNow, and SSN being a PII (not after the hack anyway...) you created an encrypted field. This SSN however can be updated from either UI, API or Data Loads. When you are testing updating the SSN from the UI things look fine. However, you'll soon notice that you aren't able to update the SSN from API or any of the Data Sources. If the problem that I just explained sounded like the one you are facing, then this post is for you. 



### We will talk about the following below:

1/ A short intro into Encrypted fields (not the edge encryption feature), and why they aren't getting updated from API or Data sources. 

2/ A hack, and I literally mean a hack to make the update on the encrypted fields from API or Data Sources. 



##### Encrypted fields and their context

If you haven't enabled encryption in ServiceNow, you can do so from the [Encryption Support](http://wiki.servicenow.com/index.php?title=Encryption_Support#gsc.tab=0). If the user doesn't have an encryption context, the user will not be able to see the encrypted field or update it. When you give a user an encryption context for an encrypted field, the user will be able to update that field. So when you are running a data load into ServiceNow using Data Sources and Transformation maps, the underlying user that ServiceNow uses to "transform" the data is _system_. _system_ doesn't have an encrypted context and so it will not be able to update any of the encrypted fields. You should be able to update the encrypted field through API, if the user who you are authenticating with does have the context of the Encrypted field you are trying to update. However if you are using a public endpoint (or) if the user doesn't have the encrypted context, then, API will not be able to update the encrypted field. 



##### Solution

<img src = "https://raw.githubusercontent.com/abhididdigi/abhididdigi.github.io/master/_images/10_29_17%2C%2015_00%20Office%20Lens.jpg">



Take a look at the rough diagram above. There is a field SSN on the incident table which is an encrypted field and only ITIL role has the encrypted context. However we want the ESS users who are accessing the portal to update the field. The below are the steps by which an ESS user will be able to update the encrypted SSN field. 



- Create a "super user" account with "encrypted context" and proper ACLs to update the Incident table. 
- When the ESS user clicks on the update, there should be two calls that need to be made to ServiceNow instance. 
  - The actual call to update all the un-encrypted fields. 
  - A second call to a specialized Processor, which takes two arguments
    - The name of a Script Include that you want to call. 
    - The arguments that the Script Include needs inorder to execute. 



When this specialized processor is being invoked, you have to authenticate using the username/password of the "super user". Below are the script components. 

**The Processor**

This is the processor that would act as a webhook. We will be calling this processor when an ESS user would update the "SSN", authenticating it with the username and password of Super user. You would need to do a  POST to this processor, and the body of the POST should contain the following key/value pairs. 

1. `script`: The name of the Script Include and it's function that you want to invoke. 
2. `params`: The parameters that you need to pass to the Script Include's initialize method. 
3. `transformer` : This is an optional parameter. Basically used to transform the data that the Script Include in the script returns. 



Also, let the _path_ of this processor will be _**/update_encrypted_fields**_

Example: 

```javascript
var BDD_Execute_Script_Controller = Class.create();
BDD_Execute_Script_Controller.prototype = {

    initialize : function(request, response, table_name, processor) {
        this.processor = processor;
        this.request = request;
        this.response = response;
        this.httpMethod = this.request.getMethod();
        this.json = new JSON();

        this.urlParameters = null;
        this.headers = null;
        this.action = null;

    },

    process : function() {

        this.processHeaders();

        this.processUrlParameters();

        if (this.httpMethod == "POST") {

            this.message = this._getMessage(this.request);

            this.processAction(this.message);

        }
    },

    processAction : function(msg) {



        if(this.httpMethod == "POST"){
            var stat = new ExecuteScript(msg).process();

            this._output(stat,200);
            return;
        }

        this._output({"error_message":"there is an error with the endpoint or there is no handler."},500);
    },

    processHeaders : function() {

        var headerObj = {};
        var headers = this.request.getHeaderNames();
        while (headers.hasMoreElements()) {
            var header = headers.nextElement();
            var value = this.request.getHeader(header);
            headerObj[header + ''] = value + '';
        }
        this.headers = headerObj;

        if (!JSUtil.nil(this.headers["x-http-method-override"])) {
            this.httpMethod = this.headers["x-http-method-override"];
        }
    },

    processUrlParameters : function() {

        var parms = this.request.getParameterNames();
        var urlParms = {};
        while (parms.hasMoreElements()) {
            var param = parms.nextElement();
            var value = this.request.getParameter(param);

            urlParms[param + ''] = value + '';
        }
        this.urlParameters = urlParms;
    },


    /**
     * parse request body into a JSON object from the http input stream
     */
    _getMessage : function(request) {

        var message;
        var is = request.getInputStream();
        var sb = GlideStringUtil.getStringFromStream(is);
        var str = sb.toString();

        var contentType = g_request.getHeader("Content-Type");
        if (contentType === "application/xml") {

            var helper = new XMLHelper("" + sb);
            message = helper.toObject();
        } else {
            var parser = new JSONParser();
            message = parser.parse(str);
        }

        return message;
    },

    _output : function(outputObj, status) {

        var contentType = this.request.getHeader("Accept");
        this._setStatus(status);


        if (contentType === "application/xml") {

            var helper = new XMLHelper();
            var message = null;
            message = helper.toXMLStr(outputObj);
            this._outputXMLResponse(message);
        } else {
            this._outputJSONResponse(outputObj);
        }
    },

    /**
     * write out the JSON object to http response with the correct content-type
     */
    _outputJSONResponse : function(response) {

        var r = this.json.encode(response);

        this.response.setHeader("Content-Type", "application/json");

        this.processor.writeOutput(r);
    },

    _outputXMLResponse : function(response) {
        this.response.setHeader("Content-Type", "text/xml");
        this.processor.writeOutput(response);
    },

    _setStatus : function(status) {
        this.response.setStatus(status);
    },

    _setBadRequestResponseStatus : function() {
        this.response.setStatus(400);
    },

    _setMethodNotAllowedStatus : function() {
        this.response.setStatus(405);
    },

    getRequestPayload : function() {
        return this.message;
    },

    type : 'Execute_Script_Controller'
};
```



**The core script include**

This script include will be called from the Processor. This will handle the bulk of the processing which is to invoke the script that sent along with passing the parameters. Below is the code. 



```javascript

/**
 * Takes in a JSON with two params, script and params, executes the Script and passes the parameters.
 * @param {JSON} msg - This contains `script` and `params`
 * @return the response after executing the script.
 */
var ExecuteScript = Class.create();
ExecuteScript.prototype = {
    initialize: function (msg) {

        // loggers, and setup.
        this.logger = Helper.log;

        this.body = msg;
        this.logger("The Script Include was called with " + new JSON().encode(this.body),this.type);
        this.script = msg["script"];
        this.params = msg["params"];
        this.transformer = msg["transformer"];

    },

    process: function () {

        // (1) check for all mandatory fields.
        if (JSUtil.nil(this.script)) {
            return this.prepareMessage("failure", "mandatory_not_found");
        }

        this.logger("Inside the process function, and all the mandatory fields are passed. ", this.type);

        var gc = GlideController; //params

        gc.putGlobal("params", this.params);


        var response = gc.evaluateString(this.script);

        this.logger("Script is executed, the response is = " + new JSON().encode(response), this.type);

        if(!JSUtil.nil(this.transformer)) {

            response = this.transformer.call(this, response);
            this.logger("The transformer wasn't empty, the response after transformation = " + new JSON().encode(response),this.type);
        }



        return response;

    },

    // preparing the error that needs to be sent back.
    prepareMessage: function (status, message) {
        var o = {};

        o["status"] = ExecuteScript.status_code[status];
        o["message"] = ExecuteScript.error_messages[message];

        return o;
    },

    type: 'ExecuteScript'
};

BDD_ExecuteScript.error_messages = {
    "mandatory_not_found": "Mandatory columns are missing.",
    "success": "Record successfully updated.",
    "logSuccess": "Successfully updated error logs.",
    "failure": "Record update failed.",
    "key_error": "key not found in table."
};

BDD_ExecuteScript.status_code = {
    "success": 1,
    "failure": -1
};
```



In order to illustrate how all the pieces work, I'll also provide you with a sample Script Include that we will execute. 

```javascript
var TestClassExecution = Class.create();
TestClassExecution.prototype = {
    initialize: function(o) {

        if(!JSUtil.nil(params)){
            // meaning we are being called from GlideEvaluator
            this.params = params;
        }else{
            this.params = o;
        }
    },


    process:function(){
      	// write your business logic to update your Encrypted fields. 
      	// and if it's a success, then return `success`, else return `failure`. 
      	var o = this.params; 
      	var table_name = o["table_name"];
      	var sys_id = o["sys_id"];
      	var values = o["values"]; // this is the column_name, value mapping of all the encrypted fields. 
      	var gr = new GlideRecord('table_name');
      	gr.get(sys_id);
        if(gr){
          for(var key in values){
            gr.setValue(key,values[key]); // set all the encrypted fields passed. 
          }
          gr.update();
          return {"status":'success'};
        }
		return {"status":'failure'};
    },

    type: 'TestClassExecution'
};
```



Here, we use a sample ScriptInclude called TestClassExecution. In real world, you will have to write your business logic of updating all your encrypted fields here.



An example of the HTTP POST. 

```javascript
URL : <instance_name>/update_encrypted_fields
METHOD : POST
BODY : {
	"script":"new TestClassExecution().process()",
    "params":{"table_name":"incident","sys_id":"dfj766869869gsdfgsdfg686879","values":{"u_ssn":"000000000"}
             }
}
AUTENTICATION: <Basic with Super User>

```





The processor will then make a call to `ExecuteScript` which will then invoke the `TestClassExecution` passing the JSON of params. 



Finally, the reason why this will work is because, when you authenticate with "super user" and then the Processor makes a call to all the subsequent Script Includes, the encryption context will be of the super user and hence the Encrypted field will be updated. 



I hope that was useful. The discussion is on [ServiceNow community](https://community.servicenow.com/thread/253214). I'm not sure ServiceNow will provide an easy way to do this, but until then, this approach worked for us. 