# ubuntu media server

```
sudo apt-get update -y
sudo apt-get install cifs-utils -y
sudo mkdir /media/shared
sudo mount -t cifs //IP_OF_FILESERVER/shared /media/shared -o username=USERNAME
cd /home/USERNAME
sudo curl -fsSL https://get.docker.com -o install-docker.sh
sudo sh install-docker.sh
sudo apt install -t $(grep VERSION_CODENAME /etc/os-release | cut -d '=' -f 2)-backports cockpit -y
sudo apt-get install cockpit-machines -y
```
open browser to https://SERVER_IP:9090/machines
```
sudo docker pull jellyfin/jellyfin
sudo docker volume create jellyfin-config
sudo docker volume create jellyfin-cache
sudo docker run -d --name jellyfin --user 1000:1000 --net=host --volume jellyfin-config:/config --volume jellyfin-cache:/cache --mount type=bind,source=/media/shared,target=/media --restart=unless-stopped jellyfin/jellyfin
sudo docker volume create prowlarr-config
sudo docker run -d --name prowlarr -p 9696:9696 -e PUID=1000 -e PGID=1000 -e UMASK=002 -e TZ="Etc/UTC" --volume prowlarr-config:/config ghcr.io/hotio/prowlarr
sudo docker volume create sonarr-config
sudo docker run -d --name=sonarr -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -p 8989:8989 --volume sonarr-config:/config -v /media/shared/TV:/tv --restart unless-stopped lscr.io/linuxserver/sonarr:latest

sudo docker volume create radarr-config
sudo docker run -d --name=radarr -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -p 7878:7878 --volume radarr-config:/config -v /media/shared/Movies:/movies --restart unless-stopped lscr.io/linuxserver/radarr:latest



```
