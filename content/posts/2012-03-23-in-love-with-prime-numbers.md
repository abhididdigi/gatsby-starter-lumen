---
layout: post
title: In Love with Prime Numbers !
--- 



 {{page.title}}
======================================================




<p>They are just beautiful. I never knew they were so good. Always lurking in the dark as if they are good-for-nothing,can&#8217;t-be-divisible-divided alien beings in the world of Mathamatics.</p>
<p>While recently solving some problems on <a href="http://www.projecteuler.net" target="_blank">projecteuler.net</a>, which is partial towards Prime Numbers, I fell in love with these aliens. Watch the blog for more, but today i will be posting a  Java Program which gives you an array list of any magnitude of Prime Numbers taking the least time.Its the least anyone can simplify it. The program is written in Java also it gives the execution time in milliseconds.</p>
<p>Note&#160;: You will have to pass the upper limit and the program gives the prime numbers between [0- upperlimie]. I haven&#8217;t used any iterator,you can use it as well for formatting your output.</p>
<pre class="prettyprint">import java.util.*;<br/>class ReturnPrime {<br/>public ArrayList primeArr;<br/>public long num;<br/>ReturnPrime(long num){<br/>primeArr = new ArrayList();<br/>this.num = num;<br/>addPrime(this.num);<br/>}<br/>public boolean isPrime(long num){<br/>if(num == 1){<br/>return false;<br/>}<br/>else if(num %2 == 0){<br/>return false;<br/>}<br/>else if(num% 3 == 0){<br/>return false;<br/>}<br/>else if(num &lt; 9){<br/>return true;<br/>}<br/>else {<br/>long r =(long) Math.floor(Math.sqrt(num));<br/>/* This is to check only the numbers that are greater than or equal to SQRT of num. This<br/>is because,If you don't find any number in this range that divides the given number,its definately prime*/<br/>/* There is no chance that the root getting divisable by 2,without the number itself getting divisable by 2,3.*/<br/>/*  The fact that any prime number greater than 3 can be written in the following format 6K(+-)1*/<br/>long _start = 5;<br/>while(_start &lt;= r){<br/>if(num%_start == 0) return false;<br/>if(num%(_start+2) == 0) return false;<br/>_start = _start + 6;<br/>}<br/>return true;<br/>}<br/><br/>}<br/><br/>public void addPrime(long num){<br/>for(long i=1; i&lt;=num; i++){<br/>if(isPrime(i)){<br/>primeArr.add(i);<br/>}<br/>}<br/>}<br/>/* This will return the entire ArrayList.You will have to use Iterator to Parse it*/<br/>public ArrayList returnPrim(){<br/>return primeArr;<br/>}<br/>/* This will return the Prime at a specific Location, say 30th prime*/<br/>public long getLocPrime(int index){<br/>Long l =  (long)primeArr.get(index);<br/>long _l = l.longValue();<br/>return _l;<br/>}<br/>public static void main(String [] args){<br/>long startTime = System.currentTimeMillis();<br/>long l = (long)Long.parseLong(args[0]);<br/>ReturnPrime rp = new ReturnPrime(l);<br/><br/>ArrayList al = rp.returnPrim();<br/>System.out.println(al);<br/>long stopTime = System.currentTimeMillis();<br/>      long elapsedTime = stopTime - startTime;<br/>      System.out.println("Elapsed time in Milliseconds:"+elapsedTime);<br/><br/>}<br/>}<br/>Written using notepad++ you would want to try it as well. Its a light-weight editor.
</pre>