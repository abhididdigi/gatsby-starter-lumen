---
layout: post
title: Scrap pages with PhantomJs and CasperJS
--- 



 {{page.title}}
======================================================




This is just an usecase for scrapping TopCoder. I was really vexed up with the ruby repo here because this gem is really a pain in the rear to install on windows. [This is](https://github.com/seri/gettc) the repo I'm taking about. 

So, I had to comeup with a solution of my own. After battling with many Python frameworks like Scrappy etc.,I finally landed on Ariya's Phantom JS headless browser. I had used Ariya's works earlier in [Jello](https://github.com/abhididdigi/Jello). This looked like a very nice use case for scrapping Topcoder.

_Note: Please use this at your own risk. I'm not sure how legal is it to scrap Top Coder._

As Phantom JS didn't have a build in scrapper, I had to rely on CasperJS, which looks very promising too.

Here is the small code:

<script src="https://gist.github.com/abhididdigi/22d0275985112df76cf1.js"></script>


Again, I reiterate the face that I'm not sure if it's really legal to scrap TopCoder. So use this at your own risk.