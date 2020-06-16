---
layout: post
title: Add headers to a report extract CSV and store it on MID
--- 



 {{page.title}}
======================================================




Finally I was able to spend sometime doing something worth sharing. I coded a new Utility called SNMIDAddHeader, that appends a header to a report which is a CSV and store the CSV on the MID Server. This was the second time I'm trying to code a feature of this sort. The first time was, when a utility by the name HP PPM wasn't able to add a header to CSV, I was tasked to created this. But I was able to create one now.

I had used [OpenCSV](http://opencsv.sourceforge.net/) Java library that does the heavy lifting of picking up the csv file and writing it back. 

Process is simple, we take the URL of the report, GET it from ServiceNow instance, store it on the MID Server. Once the file is available on MID Server, we read it back using CSVReader this time, append/update the headers and write it back.


I will in the coming days explain each part of this, so that creating new MID ScriptIncludes become easy.

If you would like to contribute to this, there are three feature requests on Github that you can start with. The code is on [Share](https://share.servicenow.com/app.do#/detailV2/5c50008e2bd621004a1e976be8da1509/overview) along with the JAR file and Update Set. You will have to first upload your JAR and then the UpdateSet. As of today the visibilty of this app is limited. It will be released to general public on Monday 11/August.