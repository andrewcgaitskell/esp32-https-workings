https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04

sudo su
apt update
apt install nginx

open ports 80 and 443

systemctl status nginx

systemctl stop nginx

systemctl start nginx

systemctl restart nginx

systemctl reload nginx

systemctl disable nginx -- not on reboot

systemctl enable nginx -- re enable on boot

