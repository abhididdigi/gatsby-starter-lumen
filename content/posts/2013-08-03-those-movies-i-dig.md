---
layout: post
title: Those movies I dig..
--- 



 {{page.title}}
======================================================




<p>I&#8217;ve been curious the &#8216;type&#8217; of movies I collect, hence over the weekend I was doing some analysis. Here is the output graph(Considering the genres of around 350+ movies).</p>

<div id="donut-example"></div>

<p><script src="http://cdnjs.cloudflare.com/ajax/libs/raphael/2.1.0/raphael-min.js"></script><script src="http://code.jquery.com/jquery-1.8.2.min.js"></script><script src="http://cdn.oesmith.co.uk/morris-0.4.1.min.js"></script></p>

<script type="text/javascript">
Morris.Donut({
  element: 'donut-example',
  data: [ { label: 'Action', value: 132 },
  { label: 'Comedy', value: 66 },
  { label: 'Adventure', value: 71 },
  { label: 'Sci-Fi', value: 53 },
  { label: 'Biography', value: 18 },
  { label: 'Drama', value: 174 },
  { label: 'History', value: 15 },
  { label: 'Romance', value: 41 },
  { label: 'Crime', value: 128 },
  { label: 'Mystery', value: 71 },
  { label: 'Thriller', value: 185 },
  { label: 'War', value: 21 },
  { label: 'Fantasy', value: 50 },
  { label: 'Horror', value: 30 },
  { label: 'Sport', value: 11 },
  { label: 'Family', value: 16 },
  { label: 'Western', value: 10 },
  { label: 'Animation', value: 11 },
  { label: 'Film-Noir', value: 1 },
  { label: 'Documentary', value: 2 },
  { label: 'Short', value: 3 },
  { label: 'Musical', value: 1 } ]
});
</script><p>I&#8217;ve used Python to read filenames, process them, strip the name into a meaningful one, send a request to IMDB or IMDBAPI, collect the data in a   <code>Output.JSON</code> file, then use Node.js to filter into manageable JSON and using Morris.js draw them. BTW, Thriller takes the first place.</p>

<p><strong>Observations and Challenges:</strong></p>

<ul><li><p>Initial challenge was to read all the file names in the same format. This was <em>almost</em> successful, around 80 file names were error-ed out, as they weren&#8217;t in the same format(even after the pruning and all).</p></li>
<li><p>Once you have the file name, it&#8217;s actually kinda difficult to look them up. This is because IMDB doesn&#8217;t have a public API, though that&#8217;s the best one we have. I&#8217;ll speak about the shortcomings below. Also, there is one API from TMDb, but the registration is painful. There are another two APIs one from Rotten Tomatoes, and the other from <a href="http://www.imdbapi.org">www.imdbapi.org</a> , but with these both you will need to have the exact name of the Movie, which isn&#8217;t the case with IMDB search API. We will be using both IMDB API and <a href="http://www.imdbapi.org">www.imdbapi.org</a>'s API. Reason for not using a Rotten Tomatoes API, is because it doesn't give you the genre names.</p></li>
<li><p>The rest is fairly simple. Though I could&#8217;ve used Python for everything, just to get a hang of file read using Node.js, I used Node to translate the Output.JSON file(explained below)  and get the JSON for graphical representation.</p></li>
</ul><p><strong>Steps Involved:</strong></p>

<ul><li><p>Read the file names from disk, and then prune them:</p>

<ul><li>Read the file names:</li>
</ul></li>
</ul><div class="gist_code_block">
         <script src="https://gist.github.com/abhididdigi/6150954.js">            </script></div>

<pre><code>  * Prune them, as per a common format:
</code></pre>

<div class="gist_code_block">

<script src="https://gist.github.com/abhididdigi/6150984.js"></script></div>

<ul><li>Make the call to the API:</li>
</ul><div class="gist_code_block">
<script src="https://gist.github.com/abhididdigi/6146857.js"></script></div>

<p>God,that was easy. When you run these scripts, your <code>output.JSON</code> will nicely get populated with the JSON information of your Movies.</p>

<p>Lastly, making using of Node.js to organize the output.JSON based on genres(Could have used Python as well)</p>

<div class="gist_code_block">
<script src="https://gist.github.com/abhididdigi/6151039.js"></script></div>

<p>the <code>arr</code> in the above script will nicely give you the count of each genre of all the movies you have,something like this.</p>

<pre><code> [ { label: 'Action', value: 132 },
 { label: 'Comedy', value: 66 },
 { label: 'Adventure', value: 71 },
 { label: 'Sci-Fi', value: 53 },
 { label: 'Biography', value: 18 },
 { label: 'Drama', value: 174 },
 { label: 'History', value: 15 },
 { label: 'Romance', value: 41 },
 { label: 'Crime', value: 128 },
 { label: 'Mystery', value: 71 },
 { label: 'Thriller', value: 185 },
 { label: 'War', value: 21 },
 { label: 'Fantasy', value: 50 },
 { label: 'Horror', value: 30 },
 { label: 'Sport', value: 11 },
 { label: 'Family', value: 16 },
 { label: 'Western', value: 10 },
 { label: 'Animation', value: 11 },
 { label: 'Film-Noir', value: 1 },
 { label: 'Documentary', value: 2 },
 { label: 'Short', value: 3 },
 { label: 'Musical', value: 1 } ]
</code></pre>

<p>Once you have those results with you, use <a href="http://www.oesmith.co.uk/morris.js/index.html">morris.js</a> to draw a Donut as above.</p>

<p>I was never surprised, I never knew I watch <strong>Drama</strong> that much!</p>