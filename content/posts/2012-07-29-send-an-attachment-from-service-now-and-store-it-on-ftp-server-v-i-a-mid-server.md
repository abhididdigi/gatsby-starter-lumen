---
layout: post
title: Send an Attachment from Service Now and store it on FTP server via MID server
--- 



 {{page.title}}
======================================================




Basically we are trying to send an attachment as soon as it is attached, to FTP Server. As I mentioned in , its always better to write the logic of writing the file to FTP in a MID server Script Includes.r_

We write a Business Rule on Attachment table, so that whenever an entry is inserted we call the MID script Include pass the attachment,that got just inserted in encoded64 format -create a file(from the information in the attachment) on FTP server using any of the Writers/Outbuffers of java and close the connection.

Name of the Business Rule :call_MIDServer_SendFile
Table: sys_attachment
When : After insert
[javascript]
fnToMidserver();
function fnToMidserver(){
   
   try{
      var gr = new GlideRecord(&quot;your_table&quot;);
      gr.get(current.table_sys_id);
      var fileName ='SNOW_'+gr.number+'_'+gr.sys_id+'_'+current.file_name;
      var ftpServer = gs.getProperty(&quot;host&quot;);
      var username = gs.getProperty(&quot;login&quot;);
      var pswrd = gs.getProperty(&quot;password&quot;);
      var port= gs.getProperty(&quot;port&quot;);
      var type=gs.getProperty(&quot;type&quot;);
      var timeout=gs.getProperty(&quot;timeout&quot;);
      var passive=gs.getProperty(&quot;passive&quot;);
      var ftpFilePath = gs.getProperty(&quot;FTP Attachment&quot;);
      /* We encode the Attachment to base64 as we cannot transfer it as a plain string.The code below handles the conversion*/

      var StringUtil = Packages.com.glide.util.StringUtil;
      var sa = new  Packages.com.glide.ui.SysAttachment();
      var binData =  sa.getBytes(current);
      var encData =StringUtil.base64Encode(binData);

/*End of the code */
      var jspr = new JavascriptProbe('Integration');
      jspr.setName('FileToMidServer'); //Any descriptive name will do
      jspr.setJavascript(&quot;var ddr = new AttachmentSender();res = ddr.execute();&quot;);
      jspr.addParameter(&quot;targetFileName&quot;,fileName);
      jspr.addParameter(&quot;encodedData&quot;,encData);
      var cred = username+&quot;:&quot;+pswrd;
      jspr.addParameter(&quot;ftpCred&quot;,cred);
      jspr.addParameter(&quot;ftpTargetServer&quot;,ftpServer);
      jspr.addParameter(&quot;ftpPort&quot;,port);
      jspr.addParameter(&quot;targetPath&quot;,ftpFilePath);
      jspr.addParameter(&quot;host&quot;,ftpServer);
      jspr.addParameter(&quot;transportMethod&quot;,type);
      jspr.create();
      gs.log('Completed: check Mid Server log');
   }
   catch(e){
      gs.log('exception in calling mid server : '+e);
      
   }
}
[/javascript]
One of the challenges we faced in sending the base64 encoded string to MID server was, We don't have the StringUtil class included with jars of MIDserver. Upon using Reflection API we came up with a different class Base64() and we use the decode method for the conversion into a plain string. 

The Script Include goes this way :

Name : Attachment Sender
[javascript]


var AttachmentSender = Class.create();

AttachmentSender.prototype = {
   initialize : function() {
          this.debug = false;
      
      //If you would like to move processed files to a target location, set the next two parameters.
      //Make sure your file name ends up being unique. We will not overwrite a file by the same name
      //in the target directory...
      this.moveProcessedFiles = true;
      this.MIDSERVER_PROCESSED_FILE_PATH = &quot;processed/&quot;;
      
      this.resLog = &quot;FTP Log: \n&quot;;
      this.log(&quot;Initializing SNDataRetriever&quot;);
      this.MIDSERVER_FILE_PATH = &quot;work/&quot;;      
      this.MIDSERVER_FILE_NAME = probe.getParameter('targetFileName');
      
      this.Encoded_Data = probe.getParameter('encodedData');
      
      this.useProxy = this.getConfigParameter(&quot;mid.proxy.use_proxy&quot;);
      if (this.useProxy){
         this.proxyHost =this.getConfigParameter(&quot;mid.proxy.host&quot;);
         this.proxyPort =this.getConfigParameter(&quot;mid.proxy.port&quot;);
         this.proxyUser =this.getConfigParameter(&quot;mid.proxy.username&quot;);
         this.proxyPass =this.getConfigParameter(&quot;mid.proxy.password&quot;);
      }
      this.user = this.getConfigParameter(&quot;mid.instance.username&quot;);
      this.password = this.getConfigParameter(&quot;mid.instance.password&quot;);
      
      var ftpCredArr = this.decryptCredentials(probe.getParameter('ftpCred'));
      this.ftpUser = ftpCredArr[0];
      this.ftpPass = ftpCredArr[1];
      this.log('ftp username : '+ftpCredArr[0]);
      this.log('ftp password : '+ftpCredArr[1]);
      this.ftpTargetServer = probe.getParameter('ftpTargetServer');
      this.ftpPort = (this.isANumber(probe.getParameter('ftpPort'))) ? (probe.getParameter('ftpPort')) : null;
      
      this.targetPath = probe.getParameter('targetPath');
      this.targetFileName = probe.getParameter('targetFileName');
      
      this.host = probe.getParameter('host');
      
      this.transportMethod = probe.getParameter('transportMethod');
      
      if (this.debug){
         this.log(&quot;\n********** Debug Info **********\nuser: &quot; + this.user + &quot;\npass: &quot; + this.password + &quot;\ntransportMethod: &quot; + this.transportMethod + &quot;\nreport: &quot; + this.reportURL+ &quot;\nhost: &quot;+ this.host);
         this.log(&quot;\n********** Proxy Info **********\nproxyNeeded: &quot; + this.proxyNeeded +&quot;\nproxyUser : &quot; + this.proxyUser + &quot;\nproxyPass :&quot; + this.proxyPass );
         this.log(&quot;\nproxyHost: &quot; + this.proxyHost + &quot;\nproxyPort:&quot; + this.proxyPort);
         this.log(&quot;\n********** FTP Info ************\nftpUser: &quot;+ this.ftpUser + &quot;\nftpPass: &quot; + this.ftpPass + &quot;\nftpPort: &quot; + this.ftpPort);
         this.log(&quot;\n********** End of Debug ********\n\n&quot;);
      }
      
   },
   
   getConfigParameter: function(parm){
      var m= Packages.com.service_now.mid.MIDServer.get();
      var config = m.getConfig();
      var res = config.getParameter(parm);
      var res2 = config.getProperty(parm);
      if (res){
         return res;
      }
      else if (res2){
         return res2;
      }
      else{
         config = Packages.com.service_now.mid.services.Config.get();
         return config.getProperty(parm);
      }
      
   },
   
   decryptCredentials: function(data) {
      var cred = new String(data);
      var e = new Packages.com.glide.util.Encrypter();
      
      var jsCred = cred + '';
      var usernamePass = e.decrypt(jsCred);
      var credArr = usernamePass.split(&quot;:&quot;, 2);
      return credArr;
   },
   
   saveToFile: function(targetPath) {
      var tmpLoc;
      var result = true;
      var strContent=new Packages.com.glide.util.Base64().decode(this.Encoded_Data);
      
      try{
         tmpLoc = this.MIDSERVER_FILE_PATH + this.MIDSERVER_FILE_NAME;
         
         var fos = new Packages.java.io.FileOutputStream(tmpLoc);
         
         for(var index=0 ; index&lt;strContent.length ; index++){
            fos.write(strContent[index].toString());
         }
         fos.close();
         
         
         this.log(&quot;File saved to: &quot; + tmpLoc);
         
      }
      catch(e){
        
         result = false;
      }
      return result;
   },
   
   copyToFTP: function() {
      var tmpLoc;
      var connectionType;
      var ftpSuccess = false;
      
      if (this.transportMethod == &quot;FTPS (Auth SSL)&quot;){
         connectionType = &quot;AUTH_SSL_FTP_CONNECTION&quot;;
         if (!this.ftpPort) {this.ftpPort = 21;}
         }
      else if (this.transportMethod == &quot;FTPS (Auth TLS)&quot;){
         connectionType = &quot;AUTH_TLS_FTP_CONNECTION&quot;;
         if (!this.ftpPort) {this.ftpPort = 21;}
         }
      else if (this.transportMethod == &quot;FTPS (Implicit SSL)&quot;){
         connectionType = &quot;IMPLICIT_SSL_FTP_CONNECTION&quot;;
         if (!this.ftpPort) {this.ftpPort = 990;}
         }
      else if (this.transportMethod == &quot;FTPS (Implicit TLS)&quot;){
         connectionType = &quot;IMPLICIT_TLS_FTP_CONNECTION&quot;;
         if (!this.ftpPort) {this.ftpPort = 990;}
         }
      else{
         connectionType = &quot;FTP_CONNECTION&quot;;
         if (!this.ftpPort) {this.ftpPort = 21;}
         }
      this.log(&quot;ConnectionType: &quot; + connectionType + &quot; and port: &quot; + this.ftpPort);
      
      var pt = new Packages.java.util.Properties();
      pt.setProperty(&quot;connection.host&quot;, this.ftpTargetServer);
      pt.setProperty(&quot;connection.port&quot;, this.ftpPort);
      pt.setProperty(&quot;user.login&quot;, this.ftpUser);
      pt.setProperty(&quot;user.password&quot;, this.ftpPass);
      pt.setProperty(&quot;connection.type&quot;, connectionType);
      pt.setProperty(&quot;connection.timeout&quot;, &quot;10000&quot;);
      pt.setProperty(&quot;connection.passive&quot;, &quot;true&quot;);
      
      try{
         var connection = Packages.org.ftp4che.FTPConnectionFactory.getInstance(pt);
         
         var fromFile = new Packages.org.ftp4che.util.ftpfile.FTPFile(this.MIDSERVER_FILE_PATH, this.MIDSERVER_FILE_NAME);
         var toFile = new Packages.org.ftp4che.util.ftpfile.FTPFile(this.targetPath, this.targetFileName);
      }
      catch(e){
         
      }
      var connectionLog = &quot;Connection Log:\n&quot;;
      var connected = false;
      try{
         connection.connect();
         this.log(&quot;Connecting to &quot; + this.ftpTargetServer + &quot; on port &quot; + this.ftpPort);
         
         connection.noOperation();
         connected = true;
      }
      catch(e){
         connectionLog += &quot;\nException in block B: &quot; + e;
         connected = false;
      }
      
      if (connected == true){
         try{
            this.log(&quot;Connected...&quot;);
            this.log(&quot;Uploading &quot; + fromFile + &quot; to &quot; + toFile);
            connection.uploadFile(fromFile, toFile);
            this.log(&quot;File successfully uploaded\n\n&quot;);
            connection.disconnect();
            ftpSuccess = true;
         }
         catch(e){
            connectionLog += &quot;\n**********FAILURE: Connection to FTP server failed**************\n&quot;;
            connectionLog += &quot;\nException in block C: \n&quot; + e;
         }
      }
      else{
         connectionLog += &quot;\n**********FAILURE: Connection to FTP server failed**************\n&quot;;
      }
      
      this.log(connectionLog);
      return ftpSuccess;
   },
   
   moveProcessedFile: function(){
      
      file = new Packages.java.io.File(this.MIDSERVER_FILE_PATH+this.MIDSERVER_FILE_NAME);// Work directory
      dir = new Packages.java.io.File(this.MIDSERVER_PROCESSED_FILE_PATH);// Move file to new (processed) directory
      success = file.renameTo(new Packages.java.io.File(dir, file.getName()));
      
      if (!success) {
         this.log('File was not successfully moved!');
      }
      else{
         this.log(&quot;File was moved from TMP directory to a processed directory&quot;);
      }
   },
   
   getLog: function() {
      return this.resLog;
   },
   
   log: function(data) {
      ms.log(data);
      this.resLog+=&quot;\n&quot;+data;
   },
   
   isANumber: function(data) {
      data = data + '';
      return ((data - 0) == data &amp;&amp; data.length &gt; 0);
   },
   
   
   execute: function() {
      var result = '';
     
      var pushRes = true;
      
      var saveRes = this.saveToFile(this.targetPath+ this.targetFileName);
      if(saveRes == true){
         pushRes = this.copyToFTP();
         result = &quot;Sucess&quot;;
         //Move tmp file to a processed directory
         if (saveRes &amp;&amp; pushRes &amp;&amp; this.moveProcessedFiles){
            this.moveProcessedFile();
         }
      }
      else{
         result = &quot;Failed&quot;;
         this.log(&quot;The report was empty?&quot;);
      }
     return result;
   },
   
   type : &quot;AttachmentSender&quot;
};
[/javascript]


All the methods are mostly taken from SNDataRetriever how ever, only one method needs mention :  moveProcessedFile. This is used to copy the file from the one directory to another(in the MID server) once the transfer to FTP is done. Also notice we used the FileOutputStream() in saveToFile() function for creating a file on the FTP server.

Acks :
1. The entire code is written by Mohammed. I am just blogging it.
2.Again, as you observe most of the code is from Service Now Guru. We just modified it and I blogged it,so that it may come handy to anybody.
