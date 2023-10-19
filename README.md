################################################################################
#
###	Edited date:	2023-10-19
###	Edited by:	Allen Wong
###	https://github.com/Avatar-British/pihole.sh
#
###	Just keep a record for future reference on the commands I used to enable the docker service in Ubuntu version 22.04
#
###	For an easy start, I just follow the exact steps shown from [theNetworkChuck] (https://www.youtube.com/watch?v=dH3DdLy574M) YouTube site, all the knowledge credits should belong to him.
###	But things change when [theNetworkChuck]'s PiHole docker image is customized and no newer version is provided, and within the PiHole container is not possible to perform update/upgrade.
###	Furthermore, my public PiHole server encountered the event of a DNS Flood Attack, which made me decide to put into practice the normal way of installing PiHole thoroughly.
###	This time, I will download the offical "pihole/pihole:latest" docker image to update my own own repository.**
#
###	Copyright (c) 2023 Avatar-British. 
#
################################################################################

```
clear
ping google.com
```
#
###	For Ubuntu version 20 or later are coming with their own DNS server on the host machine by default. If you plan to create PiHole as your own DNS server, you will need to disable the host system's default DNS server, to prevent confusion/conflict.
```
sudo systemctl stop systemd-resolved.service
sudo systemctl disable systemd-resolved.service
```
#
###	Ping Google domain to check if the DNS service is disabled, the result should be failed to resolve.
```
ping google.com
```
#
###	However, the host server still requires DNS to access the Internet. Edit the /etc/resolv.conf file to change the “nameserver” point to 8.8.8.8 IP address.
```
nano /etc/resolv.conf
```
#
###	Ping Google domain again to ensure the server are able to access Internet.
```
ping google.com
```
#
###	Get ready the Ubuntu server to install the docker
```
apt update
apt install docker.io
nano pihole.sh
```
#
##	OK, now copy the pihole.sh shell script I stored (set with Kuala Lumpur timezone setting) in my GitHub repository (https://github.com/Avatar-British/pihole.sh/blob/main/master), and pasted it inside the editor. Press "Ctrl + X" and "Yes" to save it.

##	Sidenote:
### The function of "pihole.sh" shell script is to automate call several processes:
### ① Assign a name of "pihole" given the PiHole container creates a process.
### ② Open the relevant ports (DNS, websites) for the PiHole container.
### ③ Set PiHole container timezone to match with specific geolocation.
### ④ Mount the PiHole container directories "/etc/pihole/" and "/etc/dnsmasq.d/" from the host system.
### ⑤ Set DNS server IP addresses in the PiHole container, in case the host system didn't set it in the first place. (Changed, this objective needs to look for an alternative solution)
### ⑥ Restart one time after the PiHole container completes the setup the configuration.
### ⑦ Lastly, and also most importantly, the "pihole.sh" shell script is to call(span/create) the PiHole container from a prepared docker image. For ease of calling, I cloned a copy PiHole container image from the Official docker hub site (release 2023.10.0), and put into my own repository (https://hub.docker.com/r/avatarbritish/pihole). This makes the distribution of the docker container everywhere easy, as long the docker host is accessible on the Internet.
#
###	List the directory to see if the "pihole.sh" file is created and saved in the current directory.
```
cd ~
ls
```
#
###	Try to run/execute the saved shell script.
```
sudo ./pihole.sh
```
#
###	There may be a chance that the script files fail to run, due to the script not being executed (missing the executable attribute). Use the below command to change the file attribute of the script first.
```
sudo chmod u+x pihole.sh
```
#
###	Try to run/execute the same shell script again.
```
./pihole.sh
```
#
###	If nothing goes wrong, PiHole container should be created up and running. To check, run the command below.
```
sudo docker ps
```
#
###	If PiHole container shows, please use the browser to open PiHole's admin portal (http://<same-as-host-ip-address>/admin) to see if PiHole web page appears or not.
###	You will notice that the admin password generated with the PiHole service built up is a bit hard to remember, we can access into the PiHole container to manually reset it.
###	To access into the container, use the following commands on the host machine:
```
clear
sudo docker exec -it pihole bash
```
#
### In the PiHole container environment, use the following command to reset the PiHole admin access password:
```
sudo pihole -a -p
exit
```
#
### Once changed the PiHole admin access password, you should able to use it to log in to PiHole's admin portal (http://<same-as-host-ip-address>/admin), the web page you just viewed earlier.
###	Is best you start configuring the upstream DNS (the DNS server that the PiHole server uses to resolve domain names) that your PiHole will redirect to.
##
#	Extras:
###	One thing I learned from the came across "DNS Flood attack", is that the PiHole database will crash due to oversizes database.
###	Is the advice to just remove the database only, instead of uninstalling and configuring all over again.
###	Below are the commands to remove the database file "pihole-FTL.db".


```

docker stop pihole
docker kill pihole
docker rm pihole

```
```

ls -l /etc/pihole/
rm /root/etc-pihole/pihole-FTL.db

```
```

./pihole.sh

```
```

docker exec -it pihole bash

```
```

sudo service pihole-FTL stop
sudo rm /etc/pihole/pihole-FTL.db

```

#

© 2023 GitHub, Inc.
