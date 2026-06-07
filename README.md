# DullDull
DullDull is a self-hostable replacement webpage for the ShinyShiny app which is expected to be disallowed after v138/v150 of ChromeOS on Chromebooks.  It is a very simple text editor where results can be saved or printed, and nothing more.  

**Please note, this was vibe-coded with Gemini and was checked and improved with ChatGPT.  Apologies if that offends anyone.  Please fork and improve it!  If you would like to see the chat logs from Gemini or ChatGPT I should be able to provide most of them by request.**

DullDull is intended to be self-hosted and was done so using the following:
- Proxmox v9.2.2
- LXC container of Ubuntu 26.04 LTS
- 2C/2GB RAM/1Gb shared NIC
- Cockpit v360-1 for managment (optional, recommended)
- Nginx v1.28.3 (required)
- Nginx Proxy Manager used in a separate container for reverse proxy, HTTPS, and SSL certificate management

  > **NOTE:** Within NPM, the scheme used was **http**, and the **Block Common Assets** and **Websockets support** options were turned on though these should not be required.  Regarding SSL options in NPM, a wildcard certificate was used for testing and the **Force SSL**, **HTTP/2 Support**, and **HSTS Enabled** options were turned on, though these may not be required.  HTTPS setup and SSL certificates are a subject entirely on their own and outside of the scope of this project, but HTTPS does need to be used for full functionality.

  > **NOTE: Service workers only run over HTTPS, so a local dev environment using HTTP is safe but production must enforce HTTPS to prevent MITM attacks, period.  If used in production this must ONLY be run over HTTPS**

  > **NOTE:** Additionally, in order for all features to function as intended such as the battery level widget, this must be run over HTTPS.  Using only HTTP will result in some features failing to function properly or failing to show up at all.

Because of the huge number of variations available for reverse proxies, webservers, certificate management options, etc., I will go over a super simple deployment.  This shouldn't be considered the only way or even the best way to deploy this, it is simply one way and the way that I chose to deploy it because it suits my use case and the test environment available to me.  As always, YMMV.

# Installation:

This is a very simple static webpage and can be deployed like any other webpage with Nginx or another webserver.  I will not get in to the thousands of variations but will explain briefly a simple installation:

- Create a Linux container or virtual machine or physical machine and install Ubuntu Linux
- Once installed, update using **sudo apt update && sudo apt upgrade -y** from the terminal or by SSH'ing into the server
- Install Cockpit (optional, recommended) and Nginx (required) by running **sudo apt install cockpit nginx -y**
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
   > **NOTE:** If you see the default Nginx webserver page then you have likely skipped this step
- Test the configuration for any syntax errors and reload Nginx using **nginx -t && systemctl reload nginx**
- Copy the website files from this repository to the */var/www/mywebsite.com* directory in Ubuntu
   > **NOTE:** There are several ways to get the files onto Ubuntu.  If using Cockpit Navigator from within Cockpit you can go to *Navigator* in the menu on the left hand side and simply drag and drop the index.html, sw.js, manifest.json, favicon.png, FiraMono-Regular.ttf, and RobotoSlab-Regular.ttf files into the window. You can also put them on to a file share and use wget to pull them over.  You can use **nano /var/www/mywebsite.com/index.html** to create a new file called *index.html* within the */var/www/mywebsite.com* directory and can copy and paste the contents of the index file into it, and you can do the same for sw.js and manifest.json, but note that as the .png and .ttf files are not text-based files you will not be able to create a blank file and add those contents that way. I recommend using Cockpit Navigator with Cockpit specifically for this reason and for simple site management.

From here, any changes that you need to make should be followed by the command **nginx -t && systemctl reload nginx** and you should reboot occasionally to ensure that your changes will survive reboot and that the webserver will come up on reboot.  

TTF files can be found at [Google Fonts](https://fonts.google.com/) but if you change the fonts, make sure that you have updated the index.html file accordingly.  

**_NOTE: I will mention this one last time in case you did not already read this above, you MUST use HTTPS to host this site or it will NOT be a secure way to use it and could be vulnerable to an AITM attack! Test results MUST be considered invalid if this is hosted on a non-HTTPS site.  I do not take ANY responsibility for the misuse of this PWA in any way._**


**Other license information:**

- Roboto Slab Typeface is licensed under the Apache License, Version 2.0. Copyright Google Inc.

- Fira Mono Typeface is licensed under the SIL Open Font License, Version 1.1. Copyright Carrois Corporate Design & Edenspiekermann.
