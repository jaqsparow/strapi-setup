# strapi-setup

##Follow these steps to create strapi cms on Ubuntu, Nginx, MongoDB and start server using PM2
# Update package lists
sudo apt-get update

# Update installed packages
sudo apt-get upgrade

#install nginx
sudo apt-get install nginx

#Install nodejs and npm
curl -sL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs

#install mongodb
sudo apt install mongodb
mongo

#create project folder and setup user permission
sudo mkdir -p /var/www/wali
sudo chown ubuntu:ubuntu /var/www/wali
sudo chmod 775 /var/www/wali

#go to the folder and install strapi
cd /var/www/wali
npx create-strapi-app .

/*
? Choose your default database client mongo
? Database name: wali
? Host: 127.0.0.1
? +srv connection: false
? Port (It will be ignored if you enable +srv): 27017
? Username: 
? Password: 
? Authentication database (Maybe "admin" or blank): 
? Enable SSL connection: No

Creating a project with custom database options.
Creating files.
Dependencies installed successfully.

Your application was created at /var/www/wali.

Available commands in your project:

  npm run develop
  Start Strapi in watch mode.

  npm run start
  Start Strapi without watch mode.

  npm run build
  Build Strapi admin panel.

  npm run strapi
  Display all available commands.

You can start by doing:

  cd /var/www/wali
  npm run develop
*/
sudo nano /etc/nginx/sites-available/default 
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/wali
sudo nano /etc/nginx/sites-available/wali 
sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
sudo service nginx restart

/*
server {
        listen 80;
        listen [::]:80;

        root /var/www/wali;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name example.com;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                #try_files $uri $uri/ =404;
                proxy_pass http://localhost:1337;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;


        }

}

*/

npm install
npm run develop/build to build the project

#install pm2
sudo npm install pm2 -g

#setup pm2
before that create server.js on main directory
/*server.js
const strapi = require('strapi');

strapi().start();
*/

#start pm2 to run server.js
pm2 start server.js

//Thats it. You should be able to visit exapmle.com and then example.com/admin

## If you want to turn on SSL certificate follow these extra steps 
## Install letsencrypt SSL certificate
/*

### sudo snap install core; sudo snap refresh core
### sudo snap install --classic certbot
### sudo certbot --nginx
`

*/

### sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u ubuntu --hp /home/ubuntu
