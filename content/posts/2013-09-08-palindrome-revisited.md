---
layout: post
title: Palindrome revisited.
--- 



 {{page.title}}
======================================================




<p>Probably by now, you all would have mugged up the Palindrome. Look at the following interesting question from <a href="http://community.topcoder.com/stat?c=problem_statement&amp;pm=12730">TopCoder</a></p>

<pre><code>You are given the String S. Return the smallest number of changes in which you can change S into a palindrome.
</code></pre>

<p>Let&#8217;s solve this first by Dynamic Programming, and then move on to Greedy method. The reason why will be clear soon.</p>

<p>In dynamic programming method, we divide our Problem into subproblems and slowly connect all of these to the final solution. Here is the code:</p>

<pre>
def smoothPalindrome(stri,stri2):
   if(stri == '' and stri2 == ''):
        return 0
    else:
        return diff(stri[-1:],stri2[-1:]) + smoothPalindrome(stri[:-1],stri2[:-1]);
</pre>

<pre>
def diff(x,y):  
    if(x == y):
        return 0;
     else:
    return 1;
</pre>

<pre>
def cleanPalindrome(string):
     if(len(string)%2 == 0):
         return smoothPalindrome(string[len(string)/2:],string[:len(string)/2][::-1]);
     else:
       return     smoothPalindrome(string[len(string)/2+1:],string[:len(string)/2][::-1]);
</pre>

<p>What we do in the above example is simple. We first define our sub problems as those, where in we compare the first and last character and call the function recursively. Notice how  cleanPalindrome handles both even and odd strings.</p>

<p>Now, that works good, But there is one problem with it, If you have opened that top coder problem, you will notice that, there is an extra clause, which says - When you replace one character, you have to replace all.Not one. So, that creates some problems in Dynamic Programming scenario, So let&#8217;s get greedy!</p>

<p>Here is the greedy solution:</p>

<div class="gist_code_block">
<script src="https://gist.github.com/abhididdigi/6484570.js"></script></div>

<p>Here we greedily match two strings with two pointers, and replace the alphabet with lesser number of occurrences.Hence we create a dictionary in the first step.</p>

<p>If you get zero as the answer, the string you passed is a palindrome and if you get something</p>

<pre> &gt; 0 </pre>

<p>it&#8217;s not a palindrome. But remember, the string will be converted to a palindrome.
Let me know if you have a better way to do this, and I&#8217;ll update it. Oh, btw, this <a href="http://apps.topcoder.com/wiki/display/tc/SRM%20589#GooseTattarrattatDiv1">top coder solution</a> uses Connected Components(Graph theory) to solve the same.</p>