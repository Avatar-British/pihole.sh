################################################################################
#
#	Edited date:	2023-08-12
#	Edited by:	Allen Wong
#	https://github.com/Avatar-British/pihole.sh
#
#	Just keep a record for future reference on the commands I used to enable the docker service in Ubuntu version 22.04
#   For the easy start, I just follow the exact steps shown from [theNetworkChuck] (https://www.youtube.com/watch?v=dH3DdLy574M) YouTube site, all the knowledge credits should belongs to him.**
#
#	Copyright (c) 2023 Avatar-British. 
#
################################################################################


clear

ping google.com

#	For Ubuntu version 20 or later are coming with their own DNS server on the host machine by default. If you plan to create PiHole as your own DNS server, you will need to disable the host system default DNS server, to prevent confusion/conflict.
systemctl stop systemd-resolved.service
systemctl disable systemd-resolved.service

#	Ping Google domain to check the DNS service is disabled, the result should be failed to resolve.
ping google.com

#	However, the host server still require DNS to access Internet. Edit the /etc/resolv.conf file to change the “nameserver” point to 8.8.8.8 IP address.
nano /etc/resolv.conf

#	Ping Google domain again to ensure the server are able to access Internet.
ping google.com

#	Get ready the Ubuntu server to install docker
apt update
apt install docker.io

nano pihole.sh
#	OK, now copy the pihole.sh shell script I stored (set with Kuala Lumpur timezone setting) in my GitHub repository (https://github.com/Avatar-British/pihole.sh/blob/main/master), and paste it inside the editor. Press "Ctrl+x" and "Yes" to save it.
#	Sidenote:
#   The function of "pihole.sh" shell script is to automate call several processes:
#   ① Assign a name of "pihole" given the PiHole container create process.
#   ② Open the relevant ports (DNS, websites) for the PiHole container.
#   ③ Set PiHole container timezone to match with specific geolocation.
#   ④ Mount the PiHole container directories "/etc/pihole/" and "/etc/dnsmasq.d/" from host system.
#   ⑤ Set DNS server IP addresses in the PiHole container, in case the host system didn't set it in the first place.
#   ⑥ Restart one time after PiHole container completed setup the configuration.
#   ⑦ Lastly, and also the most importantly, the "pihole.sh" shell script is to call(span/create) the PiHole container from a prepared docker image. For ease of calling, I cloned a copy PiHole container image from the "TheNetworkChuck" docker hub (with PiHole, OpenDNS and IFTTT (for Alexa usage) component ready), and put into my own repository (https://hub.docker.com/r/avatarbritish/pihole). This makes the distribution of the docker container everywhere easy, as long the docker host accessible with Internet.


#	List the directory to see if the "pihole.sh" file is created and saved in the current directory.
ls

#	Try to run/execute the saved shell script.
./pihole.sh

#	There may have a chance that the script files fail to run, due to the script cannot be execute (missing the executable attribute). Use below command to change the file attribute of the script first.
chmod u+x pihole.sh

#	Try to run/execute the same shell script again.
./pihole.sh

#	If nothing goes wrong, PiHole container should be created up and running. To check, run the command below.
docker ps

#	If PiHole container shows, please use browser to open PiHole's admin portal (http://<same-as-host-ip-address>/admin) to see is PiHole web page appear or not.

#	You will notice that the admin password generated with the PiHole service builded up is a bit hard to remember, we can access into the PiHole container to manually reset it.
#	To login access into the container, using the following commands on the host machine:
clear
docker exec -it pihole bash

#	In the PiHole container environment, use the following command to reset the PiHole admin access password:
pihole -a -p
exit

#	Once changed the PiHole admin access password, you should able to use it to login into the PiHole's admin portal (http://<same-as-host-ip-address>/admin), the web page you just viewed earlier.
#	Is best you start configure the upstream DNS (the DNS server that PiHole server to use to resolve domain names) that your PiHole will redirect to.

#	Extras:
#	The copy PiHole container image from the "TheNetworkChuck" consist a 'network.py' script programmed in Python. Those script are stored in the '/home/network' directory of the container.
#   You can study those scripts, then customize a batch domains want to be block/unblock.
#   Is easy to trigger the automation block/unblock, all you need is using the URL "http://<same-as-host-ip-address>:8080/block" to activate the blacklist in 'blockdomains.sh' script in the PiHole container, and use the URL "http://<same-as-host-ip-address>:8080/unblock" to remove the blacklist domains in 'unblockdomains.sh' script.
docker exec -it pihole bash
cd /home/network
cat network.py
nano blockdomains.sh
nano unblockdomains.sh
exit

exit

#   © 2023 GitHub, Inc.