https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04

sudo su

apt update

apt install nginx


open ports 80 and 443 using Firewall interface on GCP web site - do not do it via the Command line - it breaks SSH and VM access is broken

# Useful Commands

systemctl status nginx

systemctl stop nginx

systemctl start nginx

systemctl restart nginx

systemctl reload nginx

systemctl disable nginx -- not on reboot

systemctl enable nginx -- re enable on boot

===========

# started from these

https://www.digitalocean.com/community/tutorials/how-to-set-up-let-s-encrypt-with-nginx-server-blocks-on-ubuntu-16-04
https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04

# Steps to HTTPS on ESP32

## Step 1: godaddy

A record with example.com pointing to your server’s public IP address.

An A record with www.example.com pointing to your server’s public IP address.

## Step 2: create vm on gcp

  enable http & https
  fix ip address using standard tier
  ubuntu 1804 lts

## Open Firewall

  via gcp console allow http and https
  enable access to ports 80 and 443
  
## SSH into VM

### switch into super user
  sudo su

### install Nginx

    apt update
    apt upgrade
    apt install nginx

### check nginx server

systemctl status nginx

#### if you get an error

mkdir /etc/systemd/system/nginx.service.d

printf "[Service]\nExecStartPost=/bin/sleep 0.1\n" > /etc/systemd/system/nginx.service.d/override.conf

systemctl daemon-reload

systemctl restart nginx 

systemctl status nginx

## Notes

I did not create server block files for my domain and it worked

A separate Nginx server block file for your domain, set up by following this Nginx server blocks tutorial for Ubuntu 16.04. This tutorial will use /etc/nginx/sites-available/example.com.

## Installing Certbot

The first step to using Let’s Encrypt to obtain an SSL certificate is to install the Certbot software on your server.

Certbot is in very active development, so the Certbot packages provided by Ubuntu tend to be outdated. However, the Certbot developers maintain a Ubuntu software repository with up-to-date versions, so we’ll use that repository instead.

### add the repository.

add-apt-repository ppa:certbot/certbot

You’ll need to press ENTER to accept. Then, update the package list to pick up the new repository’s package information.

    apt update
    apt upgrade

### install Certbot’s Nginx package with apt-get.

    apt install python-certbot-nginx


### Obtain an SSL Certificate

Certbot provides a variety of ways to obtain SSL certificates, through various plugins. The Nginx plugin will take care of reconfiguring Nginx and reloading the config whenever necessary:

    sudo certbot --nginx -d example.com -d www.example.com

    sudo certbot --nginx -d acgdata.info -d www.acgdata.info
    
    certbot --nginx -d acghub.online -d www.acghub.online
    
    
This runs certbot with the --nginx plugin, using -d to specify the names we’d like the certificate to be valid for.

### Notes

If this is your first time running certbot, you will be prompted to enter an email address and agree to the terms of service. After doing so, certbot will communicate with the Let’s Encrypt server, then run a challenge to verify that you control the domain you’re requesting a certificate for.

### Settings during Certificate Generation

If that’s successful, certbot will ask how you’d like to configure your HTTPS settings.

Output
Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
-------------------------------------------------------------------------------
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
-------------------------------------------------------------------------------
Select the appropriate number [1-2] then [enter] (press 'c' to cancel):

Select your choice then hit ENTER. The configuration will be updated, and Nginx will reload to pick up the new settings. certbot will wrap up with a message telling you the process was successful and where your certificates are stored:

Output
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/example.com/fullchain.pem. Your cert will
   expire on 2017-10-23. To obtain a new or tweaked version of this
   certificate in the future, simply run certbot again with the
   "certonly" option. To non-interactively renew *all* of your
   certificates, run "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le

Your certificates are downloaded, installed, and loaded. Try reloading your website using https:// and notice your browser’s security indicator. It should indicate that the site is properly secured, usually with a green lock icon. If you test your server using the SSL Labs Server Test, it will get an A grade.

Let’s finish by testing the renewal process.
Step 5 — Verifying Certbot Auto-Renewal

Let’s Encrypt’s certificates are only valid for ninety days. This is to encourage users to automate their certificate renewal process. The certbot package we installed takes care of this for us by adding a renew script to /etc/cron.d. This script runs twice a day and will automatically renew any certificate that’s within thirty days of expiration.

To test the renewal process, you can do a dry run with certbot:

    sudo certbot renew --dry-run

If you see no errors, you’re all set. When necessary, Certbot will renew your certificates and reload Nginx to pick up the changes. If the automated renewal process ever fails, Let’s Encrypt will send a message to the email you specified, warning you when your certificate is about to expire.
Conclusion

In this tutorial, you installed the Let’s Encrypt client certbot, downloaded SSL certificates for your domain, configured Nginx to use these certificates, and set up automatic certificate renewal. If you have further questions about using Certbot, their documentation is a good place to start.

### Revoking Certificate

certbot revoke --cert-path /etc/letsencrypt/archive/${YOUR_DOMAIN}/cert1.pem


certbot revoke --cert-path /etc/letsencrypt/archive/acghub.online/cert1.pem

### New Certificate to Include Sub Domains

certbot --nginx -d acghub.online -d *.acghub.online


-------------------

cd archive/
root@acghub-web-1:/etc/letsencrypt/archive# ls
acghub.online
root@acghub-web-1:/etc/letsencrypt/archive# cd acghub.online/
root@acghub-web-1:/etc/letsencrypt/archive/acghub.online# ls
cert1.pem  chain1.pem  fullchain1.pem  privkey1.pem
root@acghub-web-1:/etc/letsencrypt/archive/acghub.online# certbot revoke --cert-path /etc/letsencrypt/archive/acghu
b.online/cert1.pem
Saving debug log to /var/log/letsencrypt/letsencrypt.log
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you like to delete the cert(s) you just revoked, along with all earlier
and later versions of the cert?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es (recommended)/(N)o: Y
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Deleted all files relating to certificate acghub.online.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations! You have successfully revoked the certificate that was located
at /etc/letsencrypt/archive/acghub.online/cert1.pem
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
root@acghub-web-1:/etc/letsencrypt/archive/acghub.online# certbot --nginx -d acghub.online -d *.acghub.online
sh: 0: getcwd() failed: No such file or directory
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Error while running nginx -c /etc/nginx/nginx.conf -t.
nginx: [emerg] BIO_new_file("/etc/letsencrypt/live/acghub.online/fullchain.pem") failed (SSL: error:02001002:system
 library:fopen:No such file or directory:fopen('/etc/letsencrypt/live/acghub.online/fullchain.pem','r') error:2006D
080:BIO routines:BIO_new_file:no such file)
nginx: configuration file /etc/nginx/nginx.conf test failed
The nginx plugin is not working; there may be problems with your existing configuration.
The error was: MisconfigurationError('Error while running nginx -c /etc/nginx/nginx.conf -t.\n\nnginx: [emerg] BIO_
new_file("/etc/letsencrypt/live/acghub.online/fullchain.pem") failed (SSL: error:02001002:system library:fopen:No s
uch file or directory:fopen(\'/etc/letsencrypt/live/acghub.online/fullchain.pem\',\'r\') error:2006D080:BIO routine
s:BIO_new_file:no such file)\nnginx: configuration file /etc/nginx/nginx.conf test failed\n',)
root@acghub-web-1:/etc/letsencrypt/archive/acghub.online# 

============================


IMPORTANT NOTES:                                                                                                   
 - Congratulations! Your certificate and chain have been saved at:                                                 
   /etc/letsencrypt/live/acghub.online/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/acghub.online/privkey.pem
   Your cert will expire on 2021-01-24. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot again
   with the "certonly" option. To non-interactively renew *all* of
   your certificates, run "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:
   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
 - We were unable to subscribe you the EFF mailing list because your
   e-mail address appears to be invalid. You can try again later by
   visiting https://act.eff.org.
   
## trying to use wildcard

root@acghub-vm-2:/home/andrew_gaitskell# certbot --nginx -d *.acghub.online
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Obtaining a new certificate
Performing the following challenges:
Client with the currently selected authenticator does not support any combination of challenges that will satisfy t
he CA. You may need to use an authenticator plugin that can do challenges over DNS.                                
Client with the currently selected authenticator does not support any combination of challenges that will satisfy t
he CA. You may need to use an authenticator plugin that can do challenges over DNS.
