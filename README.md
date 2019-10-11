# Home-Automation
Advance home automation with Home Assistant,MQTT, with local media servers and NAS support.
The repository contains my configuration and automation files.
Most of the tools used are open-source.

SETUP 

This document contains everthing you need to setup a Raspberry-Pi home server.
Follow the steps to install the following components :
1. Docker 
2. Home Assistant with hassio running insdie of docker 
3. Node-RED
4. Plex Media Server
5. Mosquitto MQTT broker
6. ESP home inside of home assistant
7. Samba Server to share the contents of your USB drive over the network.(Including samba Config)
8. Raspberry Pi- Local FM transmitter.



Congiguring Raspberry Pi
sudo apt-get update && sudo apt-get upgrade
sudo raspi-config
 - Enable VNC,SSH,Set display resolution.
lsb_release =a
 -note down the version of raspbian.

---------------------------------------------------------------------------------------------------
MOSQUITTO MQTT Broker
sudo apt install -y mosquitto mosquitto-clients
sudo systemctl enable mosquitto.service
---------------------------------------------------------------------------------------------------
Installing Node-RED
bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)
node-red-pi --max-old-space-size=256
sudo systemctl enable nodered.service
---------------------------------------------------------------------------------------------------
Home Assistant
sudo apt-get install libsndfile1-dev exfat-fuse exfat-utils bash jq curl avahi-daemon dbus samba samba-common-bin apt-transport-https ca-certificates socat software-properties-common apparmor-utils
sudo curl -sL get.docker.com | sed 's/9)/10)/' | sh
curl -sL "https://raw.githubusercontent.com/home-assistant/hassio-installer/master/hassio_install.sh" >> hassio_install.sh
sudo nano hassio_install.sh
Modify install script.
"armv7l")
        HOMEASSISTANT_DOCKER="$DOCKER_REPO/raspberrypi3-homeassistant"
        HASSIO_DOCKER="$DOCKER_REPO/armhf-hassio-supervisor"
    ;;
sudo bash hassio_install.sh
ESP Home repository link : https://github.com/esphome/hassio
------------------------------------------------------------------------------------------------------
PI FM transmitter 
git clone https://github.com/ChristopheJacquet/PiFmRds.git
cd PiFmRds/src
make clean
make
------------------------------------------------------------------------------------------------------
Raspberry Pi plex media server 

curl https://downloads.plex.tv/plex-keys/PlexSign.key | sudo apt-key add -
echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
sudo apt update
sudo apt install plexmediaserver
sudo chown -R plex: /opt/plexmedia
sudo nano /etc/default/plexmediaserver.prev (change use to pi in this file)
------------------------------------------------------------------------------------------------------
Raspberry Pi samba server
sudo lsblk
sudo mkdir /disk
sudo mount /dev/#NAME OF YOUR DRIVE# /disk
sudo nana /etc/samba/smb.conf


[Piroot]
Comment = Pi shared folder
Path = /
Browseable = yes
Writeable = Yes
only guest = no
create mask = 0777
directory mask = 0777
Public = yes
Guest ok = yes

[NAS]
Comment = Pi shared folder
Path = /disk
Browseable = yes
Writeable = Yes
only guest = no
create mask = 0777
directory mask = 0777
Public = yes
Guest ok = yes


sudo su
useradd NAME
passwd NAME
smbpasswd -a NAME
sudo /etc/init.d/smbd restart

sudo blkid (copy uuid)
sudo nano /etc/fstab
UUID= #YOUR UUID# /mnt/mydisk #FSTYPE# defaults,auto,users,rw,nofail 0 0,umask=000
Replace FSTYPE with the type of your file system, which you found in step 2 of 'Mounting a storage device' above.



