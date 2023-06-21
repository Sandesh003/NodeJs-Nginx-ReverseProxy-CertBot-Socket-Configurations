<p align=”center”>
<img src="https://github.com/Sandesh003/NodeJs-Nginx-ReverseProxy-CertBot-Socket-Configurations/assets/93026800/009c2621-b544-462b-8f88-873dddbd3b44" alt="my banner">
</p>

# Upload NodeJs Backend To Cloud Server (Ubuntu) - Configurations
This is configuration reference file which can be helpful for your next deployment

## Node Setup:-
```
sudo apt update
sudo apt install nodejs
node -v
sudo apt install npm
```
> In case of updating nodeJs
```
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
```

you need to fix path for that use below command:-
``` hash -r {newPath} ```

## GitHub Project Setup:-
get your project from GIT via git clone;

For fetching all remote branch use below command:
```
git branch -r | grep -v '\->' | sed "s,\x1B\[[0-9;]*[a-zA-Z],,g" | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
```
+ Checkout to your working branch: ```git checkout -b {branchname} ```
+ Install node_modules: ``` npm install ```
+ Make a build

## Process manager (pm2) Setup:-
```
sudo npm install pm2 -g
pm2 start //your entry file path
```
**pm2 commands:-**
```
pm2 status
pm2 logs
pm2 logs --lines 1000
pm2 stop {id}/{tag name}
pm2 start {id}/{tag name}
```

## Nginx Setup:-
```
sudo apt update
sudo apt install nginx
```
**Check ufw status**
```
sudo ufw status
```
> **NOTE:-** <br>
If it shows `inactive` then First add 22/tcp via ```sudo ufw allow 22/tcp``` after that use ``` sudo ufw enable ``` command.
> + Enabling ufw will disturb the ssh connection and after rebooting there are empty ufw list that will cause you ssh connection error and you will no longer have access to you EC2 instance.
> + Adding 22/tcp before enabling ufw will allow you to stay connected via ssh.

now you can access ufw.
```
sudo ufw app list
sudo ufw allow 'Nginx Full' (or any options you needed)
```

### Checking your Web Server
```
systemctl status nginx
```
**it will show active(running)**

+ To check weather your app is running
```
curl http://localhost
```

+ To stop your web server
```
sudo systemctl stop nginx
```

+ To start the web server when it is stopped
```
sudo systemctl start nginx
```

+ To restart the service again
```
sudo systemctl restart nginx
```

## Reverse Proxy Configurations:-

Go to ` /etc/nginx/sites-enabled/ ` and edit the default file with nano command...

**add following into default file in server** 
```
server_name your-domain.co.in;

location / {
	 proxy_pass http://localhost:8000; # your app's port
         proxy_http_version 1.1;
         proxy_set_header Upgrade $http_upgrade;
         proxy_set_header Connection 'upgrade';
         proxy_set_header Host $host;
         proxy_cache_bypass $http_upgrade;
}
```

Check your file
```
sudo nginx -t
```
+ if everything is perfect than restart your nginx server

```
sudo systemctl restart nginx
```

Check your site with your curl
```
curl http://localhost
```
+ it will now show your home route content

## CertBot Configurations:-

> Refere to [CertBot-Nginx](https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/) if you want to learn more.

**Install CertBot**
```
sudo apt-get update
sudo apt-get install certbot
sudo apt-get install python-certbot-nginx
```
> **NOTE:-** <br>
With Ubuntu 18.04 and later, substitute the Python 3 version:
```
sudo apt-get update
sudo apt-get install certbot
sudo apt-get install python3-certbot-nginx
```
+ It will do necessary changes in default.conf file of nginx and provide you certificate by running below command.

**Obtain SSL/TSL cerificate**
```
sudo certbot --nginx -d [your website address]
```

## Socket.io 
If you are having socket in your nodeJs app then this section will help you to configure it with nginx.

> For Socket.io Or Websocket Reverse Proxy : [Learn more](https://www.nginx.com/blog/nginx-nodejs-websockets-socketio/)

In the http{} configuration block (Choose Any of the following as per need)
> you can find http{} block in `/etc/nginx/nginx.conf`
```
	upstream socket_nodes {
    		ip_hash;
		    server 127.0.0.1:5000 weight=5;
    		    server 127.0.0.1:8080;       		#ideal for most cases.(Use your app's port.)
    		    server unix:/tmp/backend3;
		    server backend1.example.com weight=5;
		    server backup1.example.com  backup;
	}
```
> **NOTE:-** <br>
The nginx_http_upstream_module is used to define groups of servers that can be referenced by the proxy_pass, fastcgi_pass, uwsgi_pass, scgi_pass, memcached_pass, and grpc_pass directives.

Then change server reverse proxy url :-
```
server {
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
```

> **NOTE:-** <br>
Find more about [upstream](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#upstream)

Check your config file
```
sudo nginx -t
```

If everything is perfect than restart your nginx server
```
sudo systemctl restart nginx
```

## Upgrade Disk space EC2
In case you have a need to upgrade your disk capacity after making an EC2 instance than follow below steps.

+ Go to EC2 console , select desired ec2 instance and select Storage , then select volumn id and click on Actions and select modify volume.
+ Insert required disk space
> **NOTE:-** <br>
[Find more](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html) about expanded volume linux.

**Refere below steps for "Extend a Linux file system after resizing a volume.**


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
