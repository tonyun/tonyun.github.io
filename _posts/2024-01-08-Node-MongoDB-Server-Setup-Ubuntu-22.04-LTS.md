---
title: Node + MongoDB Server Setup Ubuntu 22.04 LTS
---
How to setup a Node.js + MongoDB environment with PM2 and Let's Encrypt

#### Login to your server as root

	ssh root@your_server_ip

#### Update the system before doing anything

	sudo apt-get update
	sudo apt-get upgrade

#### Create a non-root user and give it sudo permissions

	* adduser braitsch
	* usermod -aG sudo braitsch

#### Copy ssh key over to the non-root user home directory 
	rsync --archive --chown=braitsch:braitsch ~/.ssh /home/braitsch
	
#### Attempt to login as braitsch, if successful close root session

	ssh braitsch@your_server_ip

#### Install Node.js via nvm

	curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
	source ~/.bashrc

#### Check installation succeeded & install the latest lts version

	nvm list-remote
	nvm install --lts
	node -v

#### [Install MongoDB v6.0](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/)

	curl -fsSL https://pgp.mongodb.com/server-6.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-6.0.gpg --dearmor
	echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
	sudo apt-get update
	sudo apt-get install -y mongodb-org
	
#### Start MongoDB as a service & confirm that it's running

	sudo systemctl start mongod
	sudo systemctl status mongod
	sudo systemctl enable mongod

#### [Install & Configure NGINX](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-22-04#step-5-%E2%80%93-setting-up-server-blocks-(recommended))

	sudo apt update
	sudo apt install nginx
	
#### Setup NGINX Server Block

````sudo nano /etc/nginx/sites-available/my_domain````

	server {

		server_name my_domain.org www.my_domain.org;
		root /home/braitsch/site/server; # required to serve static images

		location / {
			proxy_pass http://localhost:8081; #nuxt app
			proxy_http_version 1.1;
			proxy_set_header Host $host;
		}

		location /api {
			proxy_pass http://localhost:8080; #express app
			proxy_http_version 1.1;
			proxy_set_header Host $host;
			client_body_buffer_size 1M; #required for image uploads
			client_max_body_size 100M; #required for image uploads
		}

		location /static {
			proxy_pass http://localhost:8080;
			try_files $uri $uri/ =404; # static asset directory
		}
	}

#### Activate NGINX Server Block

````
sudo ln -s /etc/nginx/sites-available/my_domain /etc/nginx/sites-enabled/
sudo nano /etc/nginx/nginx.conf
http {
    server_names_hash_bucket_size 64; # <- uncomment this line
}
sudo nginx -t
sudo systemctl restart nginx
````

#### Enable & Setup firewall

	sudo ufw enable
	sudo ufw allow OpenSSH
	sudo ufw allow 'Nginx Full'
	sudo ufw status

#### [Install & Setup Let's Encrypt](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-22-04)

	sudo snap install core; sudo snap refresh core
	sudo apt remove certbot
	sudo snap install --classic certbot
	sudo ln -s /snap/bin/certbot /usr/bin/certbot
	sudo certbot --nginx -d my_domain.com -d www.my_domain.com
	
#### Confirm automatic SSL cert renewal

	sudo systemctl status snap.certbot.renew.service
	sudo certbot renew --dry-run

#### Update iptables to redirect 80/443 traffic to the ports our node app will be running on

	sudo iptables -t nat -I PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
	sudo iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
	sudo iptables -A PREROUTING -t nat -i eth0 -p tcp --dport 443 -j REDIRECT --to-port 8443
	sudo sh -c "iptables-save > /etc/iptables.rules"

#### Install PM2 globally to run & monitor our Node apps

	npm install pm2@latest -g

#### Set the Server's Timezone

	sudo timedatectl set-timezone America/Los_Angeles
