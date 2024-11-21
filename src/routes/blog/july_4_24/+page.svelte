<h2>Using the 'ruby-readability' gem on Rails</h2>
<h4>July 4, 2024</h4>

<p>
	In the last post we looked at a solution for parsing the text content of a webpage, such as a news
	article, using the mozilla/readability.js node package on Rails backend.
</p>

<p>
	This time I’ll introduce the <a rel="external" href="https://github.com/cantino/ruby-readability"
		>‘readability’ gem</a
	> to do the same task – in a more Ruby way.
</p>

<p>
	<strong>
		I didn’t manage to get the gem to parse many websites, and I ended up going with the solution
		presented in the <a href="./july_1_24">previous post</a>.
	</strong>
</p>

<h3>Install the gem</h3>

<p>Run the following in you Rails project folder:</p>

<pre><code class="language-bash">bundle add ruby-readability</code></pre>

<p>
	I’ll be using Faraday for my http requests, but you could use another gem. To install Faraday,
	run:
</p>

<pre><code class="language-html">bundle add faraday</code></pre>

<h3>Using ‘ruby-readability’</h3>

<p>Get the web page with Faraday</p>

<pre><code class="language-ruby">response = Faraday.get(‘example.com/article‘)</code></pre>

<p>Parse the text content you want from the body of the page:</p>

<pre><code class="language-html">content = Readability::Document.new(response.body).content</code
	></pre>

<h3>Readability::Document instance attributes</h3>

<p>You have the following methods available:</p>

<pre>
  <code class="language-ruby">
    .images
    .author
    .title
    .content
  </code>
</pre>

<p>
	In my experience however, the parsing doesn’t always manage to identify the previous sections from
	the content.
</p>

<style src="../../../app.css"></style>
