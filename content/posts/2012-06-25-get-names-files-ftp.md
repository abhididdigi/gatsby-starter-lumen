---
layout: post
title: Get the list  of the file Names on FTP server
--- 



 {{page.title}}
======================================================




The credit for this post is given to Ishaq Khan. The script will get all the names of the files in a particular folder of FTP.

Name : GetFileNamesFTP

<pre lang="javascript">
try{
   var ftpServer = "xxxxxx.coxp.xxxx.com";
   var username = "xxxx/\xxxxx";
   var pswrd = 'xxxxx';
   var ftpFilePath = "FILELOC/\DOWNLOAD/\SND";
   var ftpFileName = 'ServiceNow_AreaData.csv';
   var pt = new Packages.java.util.Properties();
   pt.setProperty("connection.host", ftpServer);
   pt.setProperty("connection.port", "21");
   pt.setProperty("user.login", username);
   pt.setProperty("user.password", pswrd);
   pt.setProperty("connection.type", "FTP_CONNECTION");
   pt.setProperty("connection.timeout", "10000");
   pt.setProperty("connection.passive", "true");
   
   var connection = Packages.org.ftp4che.FTPConnectionFactory.getInstance(pt);
   connection.connect(); //connect
   connection.noOperation(); //keep connection alive
   
   
   
   
  
   
   gs.addInfoMessage('path is : '+ftpFilePath);
   
   var myFiles = new Packages.java.util.ArrayList();
   myFiles = connection.getDirectoryListing(ftpFilePath);
   
   gs.addInfoMessage('My files are : '+myFiles);
   
   
}
catch(e){
   gs.addInfoMessage('Exception caught :'+e);
}

</script>
</pre>

Coming posts will contain docs and explanation on  imp methods of FTP Connection Factory!