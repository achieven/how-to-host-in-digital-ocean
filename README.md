How to host in digital ocean (using npm, node, github, unix)

        1. In digital ocean - create droplet
        2. choose the standard version and rename the droplet name
        3. from command line ssh root@<ip address of droplet> and write”yes”
    4. go to your email and copy the password
    5. insert your password twice - according to request
    6. enter new password and retype it
    7. in command: adduser <my user name> and type & retype password for this username (enter requested details if you want)
        8. usermod -a -G sudo <my user name>
            9. exit and login with ssh <my user name>@<ip address of droplet> and password for this username
            10. git clone your repository and cd into it
            11. sudo apt install npm (if no default npm is supplied) (maybe it will fail and you need to do sudo apt-get update before)
            12.sudo apt install nodejs-legacy (if no default node is supplied)
            13. npm i
            14. sudo nano /etc/environment and add the line PORT=80 (make sure you node.js code can listen to process.env.PORT) press ctrl-x, Y, enter to save this file
            15. exit and login again to apply environment variable changes
            16. sudo setcap cap_net_bind_service=+ep /usr/bin/nodejs
            17. sudo npm install -g forever
            18. forever start index.js
            19. open browser on http://<ip address of droplet>
            
            
Creating swap file when run out of RAM:   (happened to me during npm i)

1. sudo fallocate -l 4G /swapfile   
2. sudo chmod 600 /swapfile   
3. sudo mkswap /swapfile   
4. sudo swapon /swapfile   
5. sudo nano /etc/fstab    
    Add this to file:  
    /swapfile   none    swap    sw    0   0
    
    
Installing free ssl and configure with nginx:    

1. set the environment port to run on port 8080 (sudo nano /etc/environment -> PORT=8080)    
2. sudo apt-get update    
3. sudo apt-get install nginx 
4. sudo ufw allow 'Nginx Full'  and sudo ufw allow OpenSSH 
5. sudo ufw enable    
6. Test - type to commanf line: "sudo systemctl start nginx" and browse the website (without a port) and see that there is a message "Welcome to nginx"
7. sudo apt-get install letsencrypt    
8. sudo letsencrypt certonly -a webroot --webroot-path=/var/www/html -d <domain_name> -d <otherdomain_name> ...    
9. sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048    
10. sudo nano /etc/nginx/snippets/ssl-<domain_name>.conf and write inside:    
ssl_certificate /etc/letsencrypt/live/<domain_name>/fullchain.pem;    
ssl_certificate_key /etc/letsencrypt/live/<domain_name>/privkey.pem;     
    
    
11. sudo nano /etc/nginx/snippets/ssl-params.conf and write inside:    

ssl_protocols TLSv1 TLSv1.1 TLSv1.2;    
ssl_prefer_server_ciphers on;    
ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";    
ssl_ecdh_curve secp384r1;    
ssl_session_cache shared:SSL:10m;    
ssl_session_tickets off;    
ssl_stapling on;    
ssl_stapling_verify on;    
resolver 8.8.8.8 8.8.4.4 valid=300s;    
resolver_timeout 5s;    
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains";    
add_header X-Frame-Options DENY;    
add_header X-Content-Type-Options nosniff;    
  
ssl_dhparam /etc/ssl/certs/dhparam.pem;      
    
12. sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak    (create a backup for this in case it's not working)
13. delete /etc/nginx/sites-available/default and create again the files with this content:   
# HTTP - redirect all requests to HTTPS:
server {
        listen 80;
        listen [::]:80 default_server ipv6only=on;
        return 301 https://$host$request_uri;
}

server {
        listen 443;
        server_name your_domain_name;

        ssl on;
        # Use certificate and key provided by Let's Encrypt:
        ssl_certificate /etc/letsencrypt/live/your_domain_name/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/your_domain_name/privkey.pem;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

        # Pass requests for / to localhost:8080:
        location / {
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-NginX-Proxy true;
                proxy_pass http://localhost:8080/;
                proxy_ssl_session_reuse off;
                proxy_set_header Host $http_host;
                proxy_cache_bypass $http_upgrade;
                proxy_redirect off;
        }
}    
14. sudo systemctl start nginx  
15. start you application (e.g forever start index.js) -  and that's it!


Setup mosh when ssh is not working anymore:    
1. ssh to the hosting machine (linux)    
2. sudo apt-get install mosh    
3. sudo iptables -I INPUT 1 -p udp --dport 60000:61000 -j ACCEPT    
4. install mosh on your local pc

