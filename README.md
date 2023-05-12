# NodeJs-Nginx-ReverseProxy-CertBot-Socket-Configurations
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
