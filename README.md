# ubuntu media server

```
sudo apt-get update -y
sudo apt-get install cifs-utils -y
sudo mkdir /media/shared
sudo mount -t cifs //IP_OF_FILESERVER/SHARE_NAME /media/SHARE_NAME -o username=USERNAME
cd /home/USERNAME
sudo curl -fsSL https://get.docker.com -o install-docker.sh
sudo sh install-docker.sh
sudo apt install -t $(grep VERSION_CODENAME /etc/os-release | cut -d '=' -f 2)-backports cockpit -y
sudo apt-get install cockpit-machines -y
```
open browser to https://SERVER_IP:9090/machines
```

```
