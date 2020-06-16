---
layout: post
title: What?! Seriously ServiceNow wiki?
--- 



 {{page.title}}
======================================================




I **llove** ServiceNow wiki. No, seriously. It has all the information arranged, and even from a usability perspective, I can now, atleast now say RTFM, because I got a good manual to read.

But this [looks a little wierd](http://wiki.servicenow.com/index.php?title=Domain_Separation_Setup#Restricting_Access_to_Domain_Selection_by_Role). ServiceNow says if you modify a particular UIMacro/UIPage for that matter any system component, it will be skipped in the next Upgrades.

But why so, now does ServiceNow wiki say that you modify the system component?

Enough with the rants, let's get on with what we are trying to do here:

---
###The Game:
You got yourself a nice little DomainSeperated ServiceNow instance, and now that there are other admins modifying stuff within different domains, naturally you really want to restrict some stuff out there so that an admin in one domain, cannot select the domains from Domain Picker, of other domains he is not part of. Sounds fair right?

"Yea so", you might think, the wiki link above does the exact same thing. No it doesn't. All the wiki link tells you is, if you need some role to have access to the Domain Picker add that role to the users who are admins, and then modify that UIMacro so that it would show this picker to those admins.

There are two potential problems from this:

1. You really aren't still handling the requirement that says, Show only those Domains that the user is part of (or) the domains that are child domains of this domain.
2. You want this UIMacro to get updated if there were an update in the next ServiceNow release, right?


Let's handle these problems one by one, and you will soon notice, the solution to problem#1 will solve the problem#2 as well.

-----
Here is the solution:

As a `before query` business rule wouldn't work, use a Global UI Script, to restrict the behaviour of the Domain Picker. Sample code below.

Here is the code(not a complete one)

```
window.setTimeout(_restrictDomains,500);
function _restrictDomains(){
try{
//$('4aa288c274b91100130473ec81cec8b0').remove()
var options = $$('select#gsft_domain_select option');
//write a GlideAjax to retrieve the names of //Domains you don't want to show, and hide them
//using $(element).remove();
var len = options.length;
for (var i = 0; i < len; i++) {
   alert(options[i]);
}
}
catch(e){
alert("Contact system admin, and deactivate UIScript by the name : RestrictDomains ");
}
}

```

A Note of caution:

The Before insert BusinessRules won't work. That Script Include that fetches the domains, will work irrespective of a Before Query Business Rule.




