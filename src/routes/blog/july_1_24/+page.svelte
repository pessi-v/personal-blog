<script>
	let problematicCode =
		"runner = NodeRunner.new(\n      <<~JAVASCRIPT\n      const { Readability } =  require('@mozilla/readability');\n      jsdom = require('jsdom');\n      const { JSDOM } = jsdom;\n      const parse = (document) => { const dom = new JSDOM(document);\n      return newReadability(dom.window.document).parse() }\n      JAVASCRIPT)";
</script>

<h2>Combining readability.js and the ‘node-runner’ gem</h2>
<h4>July 1, 2024</h4>
<p>
	I’ve been working on the second iteration of my RSS client and wanted to include the possibility
	to read articles within the reader app, in the style of Firefox’s reader view.
</p>
<p>
	I also intend to use full-text search on the feed entries in the future, so want to save the
	content data in my database.
</p>
<p>
	To these ends, I’ll use a Node.js package server-side to remove website clutter from the source
	page. To run node server-side, I’ll be using the ‘node-runner’ gem.
</p>
<p>
	In the next blog post we’ll take a look at another option for parsing content for a reader view –
	using the ‘ruby-readability’ gem.
</p>

<h4>Readability.js and building a DOM document object</h4>
<p>
	Readability.js wants to consume a DOM document object. To create one, we’ll be using the jsdom
	node package.
</p>

<h3>Required packages</h3>
<p>Make sure you’ve installed Node.js first. On Ubuntu you can install it by running:</p>
<pre>
  <code class="language-bash"></code>sudo apt update && sudo apt install nodejs npm
</pre>

<p>
	On the Rails side, we’re going to install the ‘node-runner’ gem, so, within your Rails project
	folder run:
</p>
<pre><code class="language-bash">bundle add node-runner</code></pre>

<p>
	I’ll be using Faraday for my http requests, but you could use another gem. To install Faraday,
	run:
</p>
<pre>
  <code class="language-bash"
		>bundle add faraday
</code></pre>

<p>And we’ll also be installing the node packages we want to use:</p>
<pre><code class="language-html">npm install jsdom @mozilla/readability</code></pre>

<h3>Basic usage</h3>
<p>Get the web page with Faraday</p>
<pre><code class="language-ruby">response = Faraday.get(‘example.com/article‘)</code></pre>

<p>
	Instantiate a NodeRunner object, require the node packages, add a JavaScript arrow function for
	parsing the html into a DOM document object and output the readability.js parsed content:
</p>
<pre>
  <code class="language-html" id="problematic-code">
    {problematicCode}
  </code>
</pre>

<p>
	After which we can pass the GET response body to our NodeRunner instance and receive the parsed
	content as a string:
</p>
<pre><code class="language-ruby">readability_output = runner.parse response.body</code></pre>

<p>And that’s it!</p>
