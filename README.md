<p align=”center”>
<img width=”200" height=”200" src="https://github.com/Sandesh003/NodeJs-Nginx-ReverseProxy-CertBot-Socket-Configurations/assets/93026800/9826d0a2-b852-434b-a712-f3ed18ef40ef" alt="my banner">
</p>

<p align="center">
<img src="https://github.com/Sandesh003/NodeJs-Nginx-ReverseProxy-CertBot-Socket-Configurations/assets/93026800/66aee301-b64b-4102-b841-d2c6f447ad73" alt="ScreenShot1">
</p>

<p align="center">
<img src="https://github.com/Sandesh003/NodeJs-Nginx-ReverseProxy-CertBot-Socket-Configurations/assets/93026800/982989df-54f9-484b-8415-9545b0ed2fcf" alt="ScreenShot2">
</p>

<p align="center">
<img src="https://github.com/Sandesh003/NodeJs-Nginx-ReverseProxy-CertBot-Socket-Configurations/assets/93026800/8f1227e3-328b-4e8e-a83e-f2eee29e6cbe" alt="ScreenShot3">
</p>

<p align="center">
<img src="https://github.com/Sandesh003/NodeJs-Nginx-ReverseProxy-CertBot-Socket-Configurations/assets/93026800/1d17f152-14c0-4ae7-9cab-2bbfb9708327" alt="ScreenShot4">
</p>

<p align="center">
<img src="https://github.com/Sandesh003/NodeJs-Nginx-ReverseProxy-CertBot-Socket-Configurations/assets/93026800/56493a69-782f-4496-bb20-4e5d7ca97a6f" alt="ScreenShot5">
</p>

<p align="center">
<img src="https://github.com/Sandesh003/NodeJs-Nginx-ReverseProxy-CertBot-Socket-Configurations/assets/93026800/1a7022f1-dc59-4eda-bc8c-4cc49c4aa263" alt="ScreenShot6">
</p>

# Upload Backend To Cloud Server (Ubuntu) - Configurations
This is configuration reference text file which can be helpful for your next deployment

// install node and npm:-

~ sudo apt update
~ sudo apt install nodejs
~ node -v
~ sudo apt install npm

{
	In case of updating nodeJs 
	~ sudo npm cache clean -f
	~ sudo npm install -g n
	~ sudo n stable

	you need to fix path for that use below command:-
	~ hash -r {newPath}
}

get your project from GIT via git clone;

For fetching all remote branch use below command:
~ git branch -r | grep -v '\->' | sed "s,\x1B\[[0-9;]*[a-zA-Z],,g" | while read remote; do git branch --track "${remote#origin/}" "$remote"; done

// install process manager (pm2)
~ sudo npm install pm2 -g
~ pm2 start //your entry file path

//install nginx
~ sudo apt update
~ sudo apt install nginx
~ sudo ufw app list
~ sudo ufw allow 'Nginx Full' (or any options you needed)
~ sudo ufw status (if it shows inactive then use " sudo ufw enable " command. ###IMP NOTE:- when you enable ufw and reboot your instance. First you have to add 22/tcp before enabling ufw. Following is the command
~ ufw allow 22/tcp ) 

// Checking your Web Server
~ systemctl status nginx (it will show active(running))
~ curl http://localhost ( to check weather your app is running )

// To stop your web server
~ sudo systemctl stop nginx

// To start the web server when it is stopped
~ sudo systemctl start nginx

// To stop and then start the service again
~ sudo systemctl restart nginx

// For Reverse Proxy
~ Go to " /etc/nginx/sites-enabled/ " and edit the default file with nano command...
~ add following into default file IN server --> 

server_name your-domain.co.in;

location / {
	 proxy_pass http://localhost:8000; # your app's port
         proxy_http_version 1.1;
         proxy_set_header Upgrade $http_upgrade;
         proxy_set_header Connection 'upgrade';
         proxy_set_header Host $host;
         proxy_cache_bypass $http_upgrade;
}
// Check your file
~ sudo nginx -t

if everything is perfect than restart your nginx server
~ sudo systemctl restart nginx

// Check your site with your curl
~ curl http://localhost (it will now show your home route content)

// CertBot
link:- https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/

~ sudo apt-get update
~ sudo apt-get install certbot
~ sudo apt-get install python-certbot-nginx


With Ubuntu 18.04 and later, substitute the Python 3 version:

~ sudo apt-get update
~ sudo apt-get install certbot
~ sudo apt-get install python3-certbot-nginx

Obtain SSL/TSL cerificate 
~ sudo certbot --nginx -d [your website address]


----------------------------------------------------------- Socket.io --------------------------------------------------------


For Socket.io Or Websocket Reverse Proxy:
~ https://www.nginx.com/blog/nginx-nodejs-websockets-socketio/

~ # in the http{} configuration block (Choose Any of the following as per need)
~ you can find http{} block in "/etc/nginx/nginx.conf"

	upstream socket_nodes {
    		ip_hash;
		    server 127.0.0.1:5000 weight=5;
    		server 127.0.0.1:8080;       		#ideal for most cases.(Use your app's port.)
    		server unix:/tmp/backend3;
		    server backend1.example.com weight=5;

		    server backup1.example.com  backup;
	}

~ Note:- [ The ngx_http_upstream_module is used to define groups of servers that can be referenced by the proxy_pass, fastcgi_pass, uwsgi_pass, scgi_pass, memcached_pass, and grpc_pass directives. ]

~ Then change server reverse proxy url :-
~ server {
    location / {
        proxy_pass http://socket_nodes; #{give here your upstream name which you declare in http block nginx.conf file.}
    }
    
    ### also add one more path 
    location /socket.io/ {
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_http_version 1.1;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://socket_nodes;
   }
}

~ Note:- [ https://nginx.org/en/docs/http/ngx_http_upstream_module.html#upstream ] Find more about upstream

// Check your file
~ sudo nginx -t

if everything is perfect than restart your nginx server
~ sudo systemctl restart nginx


-------------------------------------------------------- Postgres ----------------------------------------------------------------------

~ Note:- [ https://devopscube.com/install-configure-postgresql-amazon-linux/ ] Find more about postgresql installation.

~ sudo service postgresql start
~ sudo systemctl enable postgresql (This will enable postgres service to start on every service...)


------------------------------------------- upgrade Disk space EC2 ------------------------------
~ Go to EC2 console , select desired ec2 instance and select Storage , then select volumn id and click on Actions and select modify volume.
~ insert required disk space
Note: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html 

~ refere above doc for "Extend a Linux file system after resizing a volume".
~ sudo lsblk

Step : 1 [Extend the partition]
~ sudo growpart /dev/xvda 1

Step : 2 [Extend the file system]
Get the name, size, type, and mount point for the file system that you need to extend. Use the df -hT command.
~ df -hT

	2.1
	~ The commands to extend the file system differ depending on the file system type. Choose the following correct command based on the file system type that you noted in the previous step.

	[XFS file system] Use the xfs_growfs command and specify the mount point of the file system that you noted in the previous step.
	
	sudo xfs_growfs -d /

	
	2.2
	~ [Ext4 file system] Use the resize2fs command and specify the name of the file system that you noted in the previous step.

	sudo resize2fs /dev/xvda1
