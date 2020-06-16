---
layout: post
title: Handling TADDM Deleted IP Address..
--- 



 {{page.title}}
======================================================




When doing [TADDM](https://www.ibm.com/developerworks/community/wikis/home?lang=en#!/wiki/Tivoli%20Application%20Dependency%20Discovery%20Manager/page/Integrating%20TBSM%204.2%20with%20Tivoli%20Application%20Dependency%20Discovery%20Manager) direct integration, there is a use case as to how to handle the deletes of IP Interface, and re-linking of IP Address.

At the first look of it, you might thing that you will only need to deactivate those IP Addresses that are linked to a de-activated NIC. But the problem is a bit interesting than that.

What if an IP Address with the same TADDM GUID is linked to a new NIC? Meaning, the earlier NIC that this IP Address belongs to is replaced, and hence all the IPs are now pointing to the new NIC.

At the first look of this problem, I thought we will have to handle it in the  Transform Map of the IP Address. But that becomes messy because you always have to check whether the new nic you are updating the IP address with is active/inactive. 

Ideal way would be to handle it on the `update` of NIC on the IP address.

_Following functionality need to be supported:_

**Deletion:**

**1a).** When an IP Interface is TADDM-Deleted, a Script Include takes all these values and then makes them inactive.

**1b).** If there is a change to `nic` and the `nic` is inactive, then deactivate the IP Address too.

**Making the IP Active again:**

**2a).** I Initially thought, this should be handled in the Transform Maps.. But that is really long and messy. The ideal way to do this is to handle this on change of `nic`. If the `nic` has been changed to some active IP Interface, then make the IP Address active, too.

**2b).** If the IP Interface becomes active,  corresponding IP Address should be active too.
