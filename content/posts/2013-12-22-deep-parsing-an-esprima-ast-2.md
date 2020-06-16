---
layout: post
title: Deep-parsing an Esprima AST
--- 



 {{page.title}}
======================================================




<p>From last two weeks I was working on application to parse EcmaScript, popularly known as Javascript, that generates an AST. I am using <a href="http://esprima.org/">Esprima</a> to do the same. From past few weeks, I was stuck up with many (almost-good) things, that kinda decreased my IQ. Everyday, I try to write some lines to deep parse this AST, but leave after a line or two, I just wasn&#8217;t in mood. But today, finally I took some time out of my (<strike>busy</strike>) schedule and wrote this.</p>

<p><img src="http://i.imgur.com/Au51yr5.gif?1?9208"/></p>

<p>I need to deep-parse AST for two purposes:</p>

<ul><li><a href="https://github.com/abhididdigi/Jello">Jello</a> - Jelly in JavaScript to deep-parse the AST and append phase two for all the nodes which are in a block statement. For example:
for Javascript to be converted to Phase two, it should basically follow this format.</li>
</ul><pre>

{
phase:two;//Telling us the phase is two

//Lines follow
}
</pre>

<p>All the lines that follow will be rendered in Apache Jelly phase two. This needed deep parsing as I need to identify all the children of <code>phase two</code> block statement and render them differently.</p>

<ul><li>I am working on a (<strike>Super Secret </strike> NSA level project) for auditing code in Service Now platform, which needed deep parse too.</li>
</ul><p>Without further ado, here is the code.</p>

<div class="gist_code_block">
<script src="https://gist.github.com/abhididdigi/df9e4bc289beb61bbc1b.js"></script></div>

<p>The idea is to make a dictionary of all the complex look-aheads that might contain other statements, and recursively call the same function reading and processing all nodes.</p>