---
layout: post
title: The Actual Story behind rabbit, tortoise race
--- 



 {{page.title}}
======================================================




<p>One day (one of those days you get angry on the Job you are currently in,thinking about all your friends joining other companies and you still can do nothing about your current job except think) i was thinking about this great line</p>
<blockquote>
<p>Nidaname Pradhanamu (Telugu- regional language of Andra Pradesh,India)</p>
</blockquote>
<p>True Translation</p>
<blockquote>
<p>Doing things slowly is really important</p>
</blockquote>
<p><br/>I don&#8217;t deny the fact that it is really important to do things slow,and with meticulous detail that no-one else would do it better than you,but at the same time you would probably want to strike <strike>slow</strike> from the adjectives to describe the way you work in this modern times where humans are expected to work as fast as computers by the alien managers(the mother earth is already under attack,and I completely agree with the managers. If i were one, I would expect the same) who are again expected to work as fast as the computers(even this alien land of the managers&#8217; is conquered by other aliens called clients) by their alien Clients.</p>
<p>Now coming to this great story I am about to tell you,This will probably challenge your senses, and for a second will make you think I am probably bull-shitting and that I am writing this post as I have a lot of freetime and nothing better to do.But please read on. There will be something interesting at the end :) <img align="right" alt="cute Rabbit,Turtle" height="246" src="http://servicenowdiary.com/wp-content/uploads/2012/02/rabbit_turtle.jpg" width="359"/></p>
<p>One fine day,tortoise and rabbit plan to have a race. The finish line is at a 5 kms distance and once any one of the two reach the finish line,they need to light the &#8220;Winning Lantern&#8221;.The first one to do so will be declared winner. Lets assume that the rabbit can run twice as fast as a tortoise. I am the referee to this  match and I yell &#8221; On your mark,Get Set GO!&#8221; .</p>
<p>Ah, here there is a small catch. The rabbit just before the race was so overconfident that it will win that it had 6 fine shots of tequia(Actually it insisted on having another, though I had to step into the picture and stop it from having more and Saving its race-day,Thanks to me). The Race began and rabbit reached the finish line pretty confortably when the tortoise was at 2.5kms away from the finish line, but the rabbit was so drunk that it did not realize that it had just completed the race thinking it has just started the race, and then it runs again a full 5kms from start. This time, by the time it reaches half the distance our poor tortoise, being steady(as it was a teetotaler,just like me) is at the finish line and it slowly lights the &#8220;Winning Lantern&#8221; and Wins the Race.</p>
<p>Slow and &#8220;steady&#8221; wins the race. Don&#8217;t Drink is the literal meaning and more contexual meaning would be, if you target something important to your life,don&#8217;t get carried away what ever it is. Be steady on the race track or you would be allowing people who are less compotent than you are to take your position, your job and push you out. Then you can do nothing but think..&#8221;Ah.. Even I would have done that.I wasn&#8217;t focussed enough(and ofcourse other nonsensical reasons)&#8221;</p>
<p>We can use the above story to understand a way in which we can find loops in a linked list. When I first read about this method, I liked it somuch that i wanted to blog it.</p>
<p>Question&#160;: Find the best way to find a loop in a singly liked list</p>
<p>Best Solution:</p>
<p>Time Complexity :O(n)</p>
<p>Simultaneously go through the list by ones (slow iterator) and by  twos (fast iterator). If there is a loop the fast iterator will go  around that loop twice as fast as the slow iterator.  The fast iterator  will lap the slow iterator within a single pass through the cycle.   Detecting a loop is then just detecting that the slow iterator has been  lapped by the fast iterator.</p>
<pre class="prettyprint">// Best solution
function boolean hasLoop(Node startNode){
  Node slowNode = Node fastNode1 = Node fastNode2 = startNode;
  while (slowNode &amp;&amp; fastNode1 = fastNode2.next() &amp;&amp; fastNode2 = fastNode1.next()){
    if (slowNode == fastNode1 || slowNode == fastNode2) return true;
    slowNode = slowNode.next();
  }
  return false;
}<br/><br/><br/><br/> </pre>
<p>The solution is taken from <a href="http://ostermiller.org/find_loop_singly_linked_list.html.">http://ostermiller.org/find_loop_singly_linked_list.html.</a> There is a good discussion on all the methods that can be employed for finding loops in a singly linked list.</p>
<p>Now you understand why I told you that story. Don&#8217;t you?</p>