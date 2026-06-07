# dulldull
A self-hostable replacement webpage for the ShinyShiny app which is expected to be disallowed after v138/v150 of ChromeOS on Chromebooks.  It's a very very simple text editor where results can be saved or printed, and nothing more.  

**Please note, this was vibe-coded with Gemini and was checked and improved with ChatGPT.  Apologies if that offends anyone.  Please fork and improve it!  I will include those chat logs so that you can look to see what was asked and how it was answered.  I have preserved these chats to the best of my ability but something might be missing.**

DullDull is intended to be self-hosted and was done so using the following
- Proxmox v9.2.2
- LXC container of Ubuntu 26.04 LTS
- 2C, 2GB RAM, 1Gb shared NIC
- Cockpit v360-1 for managment (not required)
- Nginx v1.28.3
- Nginx Proxy Manager used in a separate container for reverse proxy, HTTPS, and SSL certificate management

Within NPM, the scheme used was **http**, and the **Block Common Assets** and **Websockets support** options were turned on though these should not be required.  Regarding SSL options in NPM, a wildcard certificate was used for testing and the **Force SSL**, **HTTP/2 Support**, and **HSTS Enabled** options were turned on, though these may not be required.  HTTPS setup and SSL certificates are a subject entirely on their own and outside of the scope of this project, but HTTPS does need to be used for full functionality.

**NOTE: Service workers only run over HTTPS, so a local dev environment using HTTP is safe but production must enforce HTTPS to prevent MITM attacks, period.  If used in production this must ONLY be run over HTTPS**

Additionally, in order for all features to function as intended such as the battery level widget, this must be run over HTTPS.  Using only HTTP will result in some features failing to function properly or failing to show up at all.

# Installation:

This is a very simple static webpage and can be deployed like any other webpage in Nginx or another webserver.  I will not get in to the millions of variations but will explain briefly a simple installation.

- Create a Linux container or virtual machine or physical machine and install Ubuntu Linux
- Once installed, update using **sudo apt update && sudo apt upgrade -y** from the terminal or by SSH'ing into the server
- Install Cockpit and Nginx by running **sudo apt install cockpit nginx -y**
- Start and enable Cockpit to run on startup using **sudo systemctl start cockpit && sudo systemctl enable cockpit**
- Open Cockpit by going to **http://YOUR_IP_ADDRESS_HERE:9090** and login
- You may also want to install [Cockpit Navigator](https://github.com/45Drives/cockpit-navigator) from the [45Drives](https://github.com/45Drives) repository using the instructions for Ubuntu as that will make adding files much easier
- Create a folder for your website on Ubuntu using **mkdir -p /var/www/mywebsite.com**
- Create and edit your custom Nginx server configuration file with **nano /etc/nginx/sites-available/mywebsite.com** replacing *mywebsite.com* with your website name
- Paste the following basic configuration, adjusting the server_name to your domain or IP address:
> Nginx Configuration File
> 
    server {
      listen 80;
      listen [::]:80;

      server_name mywebsite.com 123:456:789:123; # Replace mywebsite.com with your domain and replace 123:456:789:123 with your local IP address

      root /var/www/mywebsite.com; # Replacing mywebsite.com with your website name
      index index.html;

      location / {
          try_files $uri $uri/ =404;
      }
    }

- Enable the new site by creating a symlink to the sites-enabled directory using **ln -s /etc/nginx/sites-available/mywebsite.com /etc/nginx/sites-enabled/** replacing *mywebsite.com* with your website name
- Remove the softlink to the default Nginx webpage using **rm /etc/nginx/sites-enabled/default**
   > **Note:** If you see the default Nginx webserver page then you have likely skipped this step
- Test the configuration for any syntax errors and reload Nginx using **nginx -t && systemctl reload nginx**
- Copy the website files from this repository to the */var/www/mywebsite.com* directory in Ubuntu
   > **Note:** There are several ways to get the files onto Ubuntu.  If using Cockpit Navigator from within Cockpit you can go to *Navigator* in the menu on the left hand side and simply drag and drop the index.html, sw.js, manifest.json, favicon.png, FiraMono-Regular.ttf, and RobotoSlab-Regular.ttf files into the window. You can also put them on to a file share and use wget to pull them over.  You can use **nano index.html** from within the 



**Other license information**

- Roboto Slab Typeface is licensed under the Apache License, Version 2.0. Copyright Google Inc.

- Fira Mono Typeface is licensed under the SIL Open Font License, Version 1.1. Copyright Carrois Corporate Design & Edenspiekermann.
