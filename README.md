# Revo Node Manager
![image](https://user-images.githubusercontent.com/62940168/112182761-de730a00-8bfd-11eb-83fa-ee58bc1fba2d.png)

Revo Node Manager (RNM) is a lightweight dashboard and control system for your Revo node. 

## Features

- Extensive dashboard with general information about the node, connected peers and the blockchain
- Create rules to manage your peers
  - Ban, disconnect or log peers that waste resources, are slow or run alternative clients (e.g. BCash)
  - Set global events that trigger the execution of rules, run rules manually or set up a cron job
- Overview of all connected peers including country, ISP, client, traffic usage, supported services...
  - Ban or disconnect peers
  - Manage a list of web hoster to detect if peer is hosted or private
- Manage banned peers
  - Unban specific peers
  - Export/Import your ban list
  - Generate iptables rules (reject banned peers at OS level)
- Last received blocks information
- Last received forks (orphaned blocks / alternative chains) information
- Memory pool statitics
- Wallet overview (no functionality, information only)

## Requirements

- Revo Core 0.19.0.1+
- Web Server (Apache, Nginx, PHP Server)
- PHP 7.0.0+
  - curl extension
- Docker (Alternative to Web Server and PHP)

## Installation

1. Clone the repository.
2. Make sure `revod` (`-daemon`) is running. If you use `revo-qt` set `server=1` in the `revo.conf` file.
3. Copy `src/Config.sample.php` and remove `.sample`. Open `src/Config.php` and enter your Revo Core RPC credentials and set the RNM password.

### Manual setup

4. Make sure the RNM folder is in your web servers folder (e.g. `/var/www/html/`). If the server is publicly accessible, I recommend renaming the RNM folder to something unique. Although RNM is password protected and access can be limited to a specific IP, there can be security flaws and bugs.
5. Check that the server (e.g. `www-data`) has access (read and write) to the data folder (git will change the ownership by default).
6. Open the URL to the folder in your browser and login with the password chosen in `src/Config.php`.

### Docker

The RNM folder is mounted as volume in Docker. This way you can edit `src/Config.php` and update RNM (`git pull`) at any time without connecting to the container.

4. Change the RPC IP in `src/Config.php` to the docker network interface IP.
5. Run either `docker-compose up -d` or `docker run -d -p 8000:80 --name bnm -v ${PWD}:/var/www/html php:7.4-apache` in the RNM folder. 
6. Add the following to your `revo.conf`: 
```
rpcbind=127.0.0.1 
rpcbind=172.17.0.1 
rpcallowip=0.0.0.0/0
```
7. RNM should now be accessible under http://server-ip:8000. 

## Security

- All pages and control functionality are only accessible for logged-in users. The only exception is if you use the Rules cron job functionality. But a password based token is required and the functionality is only able to apply rules.
- Access to RNM is by default limited to localhost. This can be expanded to a specific IP or disabled. If disabled, make sure to protect the RNM folder (.htaccess or rename it to something unique that an attacker will not guess). An attacker could "guess" your password, since there is no build-in brute force protection.
- The `data` folder contains your rules, rule logs and geo information about your peers. Make sure to protect (e.g. `chmod -R 700 data`) peer information if your web server is publicly accessible. The previously mentioned IP protection doesn't work here. If you use `Apache` you are fine, since the folder is protected with `.htaccess` (make sure `AllowOverride All` is set in your Apache config file).

