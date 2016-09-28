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
            10. git clone your repository
            11. sudo apt install npm (if no default npm is supplied)
            12.sudo apt install nodejs-legacy (if no default node is supplied)
            13. npm i
            14. sudo nano /etc/environment and add the line PORT=80 (make sure you node.js code can listen to process.env.PORT) press ctrl-x, Y, enter to save this file
            15. exit and login again to apply environment variable changes
            16. sudo setcap cap_net_bind_service=+ep /usr/bin/nodejs
            17. sudo npm install -g forever
            18. forever start index.js
            19. open browser on http://<ip address of droplet>
            
            
Creating swap file when run out of RAM:   

1. sudo fallocate -l 4G /swapfile   
2. sudo chmod 600 /swapfile   
3. sudo mkswap /swapfile   
4. sudo swapon /swapfile   
5. sudo nano /etc/fstab    
    Add this to file:  
    /swapfile   none    swap    sw    0   0
