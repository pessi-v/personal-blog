<div class="post">
	<h2>Install Dokku and deploy your Rails project with git</h2>
	<h4>September 30, 2024</h4>

	<p>
		This time we’ll do a speedrun of installing Dokku on a remote server, deploying a Rails project
		with Postgresql and configuring a domain name. You should already have a remote server with
		Ubuntu 20.04/22.04/24.04 or Debian 11+ x64 for this.
	</p>

	<p>Let’s go!</p>

	<h3>Installing Dokku</h3>

	<p>Ssh to your remote server and install Dokku using the Dokku install script:</p>

	<pre>
    <code class="language-bash">
      ssh root@YOUR-SERVER-IP
      wget -NP . https://dokku.com/install/v0.35.4/bootstrap.sh
      sudo DOKKU_TAG=v0.35.4 bash bootstrap.sh
    </code>
  </pre>

	<p>
		Once the installation is complete, you should configure an ssh key and set your global domain:
	</p>

	<pre>
    <code class="language-bash">
      cat ~/.ssh/authorized_keys | dokku ssh-keys:add admin
      dokku domains:set-global YOUR-SERVER-IP
    </code>
  </pre>

	<h3>Deploying your Rails project</h3>

	<p>Still on your Dokku host, create a new Dokku app:</p>

	<pre><code class="language-bash">dokku apps:create YOUR-PROJECT-NAME</code></pre>

	<p>Install the Dokku Postgresql plugin, create a database and link it to your project:</p>

	<pre>
    <code class="language-bash">
      sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git
      dokku postgres:create railsdatabase
      dokku postgres:link railsdatabase YOUR-PROJECT-NAME
    </code>
  </pre>

	<p>
		Then, on your local development machine, go to your Rails project folder, set up the Git remote
		and push your code to your new Dokku instance:
	</p>

	<pre>
    <code class="language-bash">
      git remote add dokku dokku@YOUR-SERVER-IP:YOUR-PROJECT-NAME
      git push dokku main
    </code>
  </pre>

	<h3>Configuring your DNS settings for your domain</h3>

	<p>
		If you’ve just bought a domain name, you should delete the existing DNS records for the domain.
		What you’ll need, is a record with the following configuration:
	</p>

	<pre>
    <code class="language-json">
      Type: A
      Host: The domain name you’ve bought, or a subdomain you might want to use, i.e. YOUR-DOMAIN-OR-SUBDOMAIN-NAME
      Answer: YOUR-SERVER-IP
      TTL: Doesn’t really matter, can be 5 min / 300 seconds
    </code>
  </pre>

	<p>Then go back to your server’s ssh session and run:</p>

	<pre><code class="language-bash"
			>dokku domains:add YOUR-PROJECT-NAME YOUR-DOMAIN-OR-SUBDOMAIN-NAME</code
		></pre>

	<p>And the last thing is to remember to setup your Rails database and its migrations:</p>

	<pre><code class="language-bash">dokku run rails db:setup</code></pre>

	<h3>Maintaining your Dokku app</h3>

	<p>
		With this setup, you'll have to remember to push your code updates to Dokku, when you want to
		update your running app.
	</p>

	<pre>
		<code class="language-bash">
			git push dokku
		</code>
	</pre>

	<p>
		Should you need to debug your application, you can read the logs by logging in to your server
		and running the following:
	</p>

	<pre>
		<code class="language-bash">
			dokku run YOUR-PROJECT-NAME logs -t
		</code>
	</pre>

	<p>That is all! Hope it worked!</p>
</div>

<style src="../../../app.css"></style>
