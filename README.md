# dulldull
A self-hostable replacement webpage for the ShinyShiny app which is expected to be disallowed after v138/v150 of ChromeOS on Chromebooks.  It's a very very simple text editor where results can be saved or printed, and nothing more.  

**Please note, this was vibe-coded with Gemini and was checked and improved with ChatGPT.  Apologies if that offends anyone.  Please fork and improve it!  I will include those chat logs so that you can look to see what was asked and how it was answered.  I have preserved these chats to the best of my ability but something might be missing.**

DullDull is intended to be self-hosted and was done so using the following
- Proxmox v9.2.2
- LXC container of Ubuntu 26.04 LTS
- 4C, 4GB RAM, 25G shared NIC
- Cockpit v360-1 for managment (not required)
- Nginx v1.28.3
- Nginx Proxy Manager used in a separate container for reverse proxy, HTTPS, and SSL certificate management

Within NPM, the scheme used was **http**, and the **Block Common Assets** and **Websockets support** options were turned on though these should not be required.  Regarding SSL options in NPM, a wildcard certificate was used for testing and the **Force SSL**, **HTTP/2 Support**, and **HSTS Enabled** options were turned on, though these may not be required.  HTTPS setup and SSL certificates are a subject entirely on their own and outside of the scope of this project, but HTTPS does need to be used for full functionality.

**NOTE: Service workers only run over HTTPS, so a local dev environment using HTTP is safe but production must enforce HTTPS to prevent MITM attacks, period.  If used in production this must ONLY be run over HTTPS**

Additionally, in order for all features to function as intended such as the battery level widget, this must be run over HTTPS.  Using only HTTP will result in some features failing to function properly or failing to show up at all.



**Other license information**

-Roboto Slab Typeface is licensed under the Apache License, Version 2.0. Copyright Google Inc.

-Fira Mono Typeface is licensed under the SIL Open Font License, Version 1.1. Copyright Carrois Corporate Design & Edenspiekermann.
