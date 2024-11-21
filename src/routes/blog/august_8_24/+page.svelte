<div class="post">
	<h2>Digital Ocean + Yunohost + Rocket.chat simple setup</h2>
	<h4>August 8, 2024</h4>

	<p>Here’s the steps I followed to setup a self-hosting solution for Rocket.chat:</p>

	<h3>Digital Ocean droplet</h3>

	<p>
		I ended up needing an instance with 4GB RAM (2 vCPUs 4GB / 50GB Disk). For Yunohost the image
		has to be Debian 11
	</p>

	<h3>Yunohost</h3>

	<p>ssh to your new droplet:</p>

	<pre><code class="language-bash">ssh root@&lt;your droplet ip&gt;</code></pre>

	<p>Change to /tmp:</p>

	<pre><code class="language-bash">cd /tmp</code></pre>

	<p>Get the Yunohost install script:</p>

	<pre><code class="language-bash">wget -O yunohost https://install.yunohost.org/</code></pre>

	<p>Run the script:</p>

	<pre><code class="language-html">sudo /bin/bash yunohost</code></pre>

	<p>Let Yunohost overwrite and set any configurations it prompts for.</p>

	<p>
		Login to your new Yunohost instance with a browser by going to the droplet IP address and finish
		the setup there. Setup your domain (optional)
	</p>

	<p>
		If you already have purchased a domain name, you can configure your Yunohost and Rocket.chat
		instances to use them by editing the ‘domains’ section in your Yunohost admin.
	</p>

	<p>For my case, I set up two subdomains: yunohost.mydomain.com and chat.mydomain.com.</p>

	<p>I then configured two A Records at my domain registrar, which in this case was porkbun.com:</p>

	<pre>
		<code class="language-json">
			Host: yunohost.&lt;your domain&gt;
			Answer: &lt;your droplet ip&gt;
			TTL: 600
	
			Host: chat.&lt;your domain&gt;
			Answer: &lt;your droplet ip&gt;
			TTL: 600
		</code>
	</pre>

	<p>
		Propagation took maybe a couple of minutes, after which I could access both my Yunohost and my
		Rocket.chat instances with the given addresses.
	</p>

	<p>After you’ve setup Yunohost, you can ssh to your server if needed with:</p>

	<pre>
		<code class="language-json">
			ssh &lt;your Yunohost admin user name&gt;@&lt;your droplet ip&gt;
			password: &lt;your yunohost admin user password&gt;</code
		>
	</pre>

	<h3>Rocket.chat setup</h3>

	<p>When logging in to Rocket.chat for the first time, you’ll be run through a basic setup.</p>

	<p>
		After the setup, the one thing I still needed to do was setup an email account for outgoing
		emails. I used a Gmail account for this.
	</p>

	<p>
		For this use case, I needed an “App Password” from my Google Account settings first. Make sure
		you’ve set up 2-Factor Authentication (2FA), then go to
		https://myaccount.google.com/apppasswords and generate and save a password for your Rocket.chat
		instance.
	</p>

	<p>
		Logged in with your Rocket.chat admin user, first check your admin email address is correctly
		set by navigating to Users on the menu.
	</p>

	<p>When the admin user email is set, go to Settings > Email > SMTP and set the following:</p>

	<pre>
		<code class="language-json">
			Protocol: smtp
			Host: smtp.gmail.com
			Port: 587
			IgnoreTLS: false
			Pool: true
			Username: &lt;your gmail address&gt;
			Password: &lt;your new app password&gt;
			From Email: &lt;your gmail address&gt;
			</code>
	</pre>

	<p>
		Save your changes and verify that it works by sending a test mail to your admin user. Let’s
		Encrypt Certificates
	</p>

	<p>
		In your Yunohost admin area you can set up Let’s Encrypt SSL Certificates for your subdomains.
		Go to Domains > your domain > Certificate > Install Let’s Encrypt Certificate.
	</p>

	<p>
		In my case, there were some warnings, so I opened up the diagnosis page. The diagnosis indicated
		a few warnings and one issue, but since everything seemed to be working in practice, I decided
		to switch on “Ignore diagnosis checks” and proceed with installing the certificates. There
		seemed to be no further problems a and my certificates appear to function like they should!
		Wrap-up
	</p>

	<p>
		I’m very impressed with Yunohost so far. I had tried a couple of alternatives already, including
		CapRover, but the experience with Yunohost was extremely smooth up to this point!
	</p>
</div>

<style src="../../../app.css"></style>
