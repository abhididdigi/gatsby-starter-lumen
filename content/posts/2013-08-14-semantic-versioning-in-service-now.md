---
layout: post
title: Semantic Versioning in Service Now
--- 



 {{page.title}}
======================================================




Naming the update sets is a very tricky and confusing process. Say you developed a functionality today, and then added that to an update set, then after a month fixed some enhancements , and then bug fixes.We need to follow some very robust mechanism so that we can trace our Updates back. The reminder of the section will concentrate on how well you can do this to avoid any confusion, using<a href="http://semver.org/"> Semantic Versioning</a>

The current versioning format that I follow usually is:

[code] the-name-of-the-client/module/submodule/anyrandomstring 


the-name-of the client - Is the name of the client that I work for, Not the name of the company i work with.
submodule - Is anything like CM/IM/SC/PM
anyrandomstring - Any random string which will tell what I'm doing - human readable form.

[/code]

and let me tell you this is a strict no-no(read on).

Now, the above way of versioning is way too confusing, You really don't have a way to identify what enhancement is going on, from the name, and is there any changes to that enhancement and/or is that a bug fix to that enhancement.

Semantic Versioning (if you have opened the above link) states that :
<ul>
<li>MAJOR version when you make incompatible API changes,</li>
<li>MINOR version when you add functionality in a backwards-compatible manner, and</li>
<li>PATCH version when you make backwards-compatible bug fixes.</li>
</ul>

Though in Service Now we really don't have a API that we are working with, hence modifying the semantic versioning and extending it to Service Now.

The Update set should follow the following naming conventions:

<ul>
	<li>A Major Release is one, where you create a new Module/Application using App Creator or otherwise. Adding functionality will not be considered a Major Release. Adding a new Service to Service Catalog will NOT be considered a Major release, Unless - Unless its a new process in itself, and it won't comply with the rules of Service Catalog.</li>


	<li>A Minor Release is the one where you add enhancements to existing modules. Example: Customizing an Incident form will be termed as a minor release.</li>


	<li>A Patch is the one where you fix an issue corresponding to an enhancement.</ul></li>





I will assume your project does track enhancement numbers and bug numbers. 

The best way to name an update set in my opinion  is like this:

<ul>
	<li>[code]name_of_your_company/MODULENAME/ [/code] - This is a Major release.</li>

	<li>
[code]name_of_your_company/MODULENAME/ENHANCEMENTNAME/ [/code] - This is a Minor Release.
</li>


	<li>[code]name_of_your_company/MODULENAME/ENHANCEMENTNUMBER/BUGNUMBER[/code] - This is a patch.</li>

</ul>


Also remember, always your Update sets MUST contain the description of the corresponding Enhancements/Bugs.

name_of_your_company - The name of company you work for. Not the client.
MODULENAME- Name of the Module, Service Catalog/Incident/Problem - any other application/module name.
ENHANCEMENTNUMBER - Enhancement Number you use to track an enhancement.
BUGNUMBER- BUG NUMBER you use to track your bugs.

You might notice that from the above initial configuration, I've eliminated the CLIENT NAME from the UPDATE Set, because, You don't really need to name it by the client name- Because it's her instance you are using to customize anyway. Instead use the name of the Company/Consultancy customizing it, so that if in case, the project goes into maint phase/changes hands, and goes to to some other Consultants/Company, then they will know which update set belongs to them, and which belongs to you(your company).


That's not the full story. Sometimes, in service Now, we do adhoc fixes, on QA directly.In that case, you should name your Update sets in this manner:

<ul>
	<li>[code]name_of_your_company/MODULENAME/InstanceFrom-&gt;InstanceTo [/code] - This is a Major release.</li>

	<li>[code]name_of_your_company/MODULENAME/ENHANCEMENTNAME/InstanceFrom-&gt;InstanceTo [/code] - This is a Minor Release.</li>

	<li>[code]name_of_your_company/MODULENAME/ENHANCEMENTNUMBER/BUGNUMBER/InstanceFrom-&gt;InstanceTo[/code] - This is a patch</ul></li>





The instancefrom->instanceto can be eliminated when you move update sets from Dev to other instances, because its implicit.


Let me know what you think in comments!

 