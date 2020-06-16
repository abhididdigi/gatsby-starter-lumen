---
layout: post
title: Getting the names of Fields that changed on Server side before Update
--- 



 {{page.title}}
======================================================




I'm not sure if this is something you already know. But I found this when I was going through some old Business Rules.

In this post we will be talking about getting the variables which change in a **Before update** Business Rule(or for that matter any Server side code).

I've not seen a more elegant way to do this. This also touches one of the use cases for j2js written by Sligtly Loony ( Tom ).

Here is the code : 

<script src="https://gist.github.com/abhididdigi/11226875.js"></script>

J2Js is a wonderful Script Include. Don't forget to check that out!