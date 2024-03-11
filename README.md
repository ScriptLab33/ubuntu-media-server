# ubuntu media server

Cockpit: https://SERVER_IP:9090/machines
Prowlarr: http://SERVER_IP:9696
Sonarr: http://SERVER_IP:8989
Radarr: http://SERVER_IP:7878/

```
sudo apt-get update -y
sudo apt install -t $(grep VERSION_CODENAME /etc/os-release | cut -d '=' -f 2)-backports cockpit -y
sudo apt-get install cockpit-machines -y
sudo apt-get install cifs-utils -y
mkdir /home/USERNAME/shared
sudo nano /etc/fstab
  >> ADD: //IP_OF_FILESERVER/shared /home/USERNAME/shared cifs username=USERNAME,noauto,rw,users 0 0
mount -t cifs //IP_OF_FILESERVER/shared /home/USERNAME/shared -o username=USERNAME
cd /home/USERNAME/shared
sudo curl -fsSL https://get.docker.com -o install-docker.sh
sudo sh install-docker.sh
sudo groupadd docker
sudo usermod -aG docker $(whoami)

docker pull jellyfin/jellyfin

docker volume create jellyfin-config
docker volume create jellyfin-cache
docker run -d --name jellyfin --user 1000:1000 --net=host --volume jellyfin-config:/config --volume jellyfin-cache:/cache --mount type=bind,source=/home/USERNAME/shared,target=/media --restart=unless-stopped jellyfin/jellyfin

docker volume create snr-config
docker run -d --name=sonarr -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -p 8989:8989 --volume snr-config:/config --mount type=bind,source=/home/USERNAME/shared/TV,target=/tv --mount type=bind,source=/home/USERNAME/shared/torrents/downloads/complete,target=/downloads --restart unless-stopped lscr.io/linuxserver/sonarr:latest

docker volume create prw-config
docker run -d --name prowlarr -p 9696:9696 -e PUID=1000 -e PGID=1000 -e UMASK=002 -e TZ="Etc/UTC" --volume prw-config:/config ghcr.io/hotio/prowlarr

docker volume create rdr-config
docker run -d --name=radarr -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -p 7878:7878 --volume radarr-config:/config -v /home/USERNAME/shared/Movies:/movies --restart unless-stopped lscr.io/linuxserver/radarr:latest

docker volume create transm-config
docker run -d --name=transmission -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -e USER=USERNAME -e PASS=USERPASSWORD -p 9091:9091 -p 51413:51413 -p 51413:51413/udp --volume transm-config:/config -v /home/USERNAME/shared/torrents/downloads:/downloads -v /home/USERNAME/shared/torrents/watched:/watch --restart unless-stopped lscr.io/linuxserver/transmission:latest



```
