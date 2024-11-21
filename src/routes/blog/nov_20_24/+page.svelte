<div class="post">
	<h2>Deploying Streamlit on EC2, with nginx for external HTTP requests</h2>
	<h4>November 20, 2024</h4>

	<p>
		Today I'm writing a description of how I deployed Streamlit version 1.40.1 on Amazon AWS EC2,
		made my process run in the background using systemd and nohup, and set up nginx to forward
		requests to port 80 to my Streamlit app.
	</p>

	<p>
		This is in preparation to setting up a subdomain for my app, on which I'll do a writeup soon.
	</p>

	<h3>Amazon AWS EC2 instance</h3>

	<p>
		I won't get into too much detail on how to set up an EC2 instance. What I will say, is that I
		ended up needing more than 10GB of disk space, with an Ubuntu image and a pretty basic Streamlit
		app.
	</p>

	<p>
		I wanted the app to hibernate when it's not in use, and in order to enable hibernation, I had to
		enable encryption of the disk.
	</p>

	<p>
		You should generate an ssh key, or use an existing key you might have created on AWS before.
		With this key, you can later ssh to your instance.
	</p>

	<p>
		Configure the inbound rules for the security group of your instance to have the following rules:
	</p>

	<pre>
    <code class="language-json">
      type: HTTP
      protocol: TCP
      Port range: 80
      Source: Anywhere-IPv4
  
      type: SSH
      protocol: TCP
      Port range: 22
      Source: Anywhere-IPv4
  
      type: Custom TCP
      protocol: TCP
      Port range: 8501
      Source: Anywhere-IPv4
  
      type: HTTPS
      protocol: TCP
      Port range: 443
      Source: Anywhere-IPv4
    </code>
  </pre>

	<p>
		You will find the Public IPv4 address in your instance details after the instance has been
		created. With the ssh key .pem file created during the setup of your instance, the SSH port 22
		allowed, and your instance ip address, you can login to your instance with ssh:
	</p>

	<pre>
    <code class="language-zsh">
      ssh -i &lt;path to your .pem file&gt; ubuntu@&lt;your ec2 instance ip&gt;
    </code>
  </pre>

	<h3>Setting up your project on the EC2 instance</h3>

	<p>
		Login with ssh to your instance, clone your project from GitHub, then install the python3-venv
		package and create a virtual environment, in which you can install the requirements of your
		Streamlit app.
	</p>

	<pre>
    <code class="language-bash">
      git clone  &lt;the web url for your Streamlit project's GitHub repo&gt;
  
      sudo apt update
      sudo apt install python3-venv
  
      cd &lt;your project folder&gt;
      python3 -m venv environment
  
      source environment/bin/activate
      pip3 install -r requirements.txt
    </code>
  </pre>

	<h3>Make the Streamlit app run in the background at startup</h3>

	<p>
		First, we'll create a launcher script, run.sh, where we use the 'nohup' command to run your
		Streamlit process in the background:
	</p>

	<pre>
    <code class="language-bash">
      #!/bin/bash
      cd /home/ubuntu/&lt;your project folder&gt;
      source environment/bin/activate
      nohup streamlit run main.py
    </code>
  </pre>

	<p>
		Then we'll create a systemd .service file in /etc/systemd/system/&lt;your project
		name&gt;.service:
	</p>

	<pre>
    <code class="language-bash">
      [Unit]
      Description=Streamlit Service
      After=network.target
  
      [Service]
      User=ubuntu
      WorkingDirectory=/home/ubuntu/&lt;your project folder&gt;
      ExecStart=/bin/bash /home/ubuntu/&lt;your project folder&gt;/run.sh
  
      [Install]
      WantedBy=multi-user.target
    </code>
  </pre>

	<p>
		After which we'll reload systemd configuration to include your new .service file, launch your
		service and enable it for startup:
	</p>

	<pre>
    <code class="language-bash">
      sudo systemctl daemon-reload
      sudo systemctl start &lt;your project name&gt;
      sudo systemctl enable &lt;your project name&gt;
    </code>
  </pre>

	<p>You'll want to verify that the process is loaded and running:</p>

	<pre>
    <code class="language-bash">
      systemctl status &lt;your project name&gt;
    </code>
  </pre>

	<h3>Nginx installation and config</h3>

	<p>Let's install nginx on the system and make it launch on startup:</p>

	<pre>
    <code class="language-bash">
      sudo apt install nginx
      sudo systemctl start nginx
      sudo systemctl enable nginx
    </code>
  </pre>

	<p>We'll add some configuration:</p>

	<pre>
    <code class="language-bash">
      sudo nano /etc/nginx/sites-available/default
    </code>
  </pre>

	<p>Make this the content of the file:</p>

	<pre>
    <code class="language-json">
      server &lcub;
        listen 80;
        server_name &lt;your EC2 IP address&gt;;
  
        location / &lcub;
          proxy_pass http://localhost:8501;  # Replace the port number with your Streamlit's port
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
  
          # WebSocket support for Streamlit
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection "upgrade";
        &rcub;
      &rcub;
    </code>
  </pre>

	<p>Then restart Nginx:</p>

	<pre>
    <code class="language-bash">
      sudo systemctl restart nginx
    </code>
  </pre>

	<p>Hope you got your app running with these instructions!</p>
</div>

<style src="../../../app.css"></style>
