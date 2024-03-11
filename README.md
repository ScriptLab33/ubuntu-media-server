# Ubuntu Media Server Setup Guide

This guide provides instructions for setting up a media server on Ubuntu, utilizing popular tools like Prowlarr, Sonarr, Radarr, Jellyfin, and Transmission. Ideal for both novice and experienced users, this setup enables efficient management and streaming of media content.

## Prerequisites

- Ubuntu Server (version 20.04 or later recommended)
- Basic understanding of Linux command line and Docker
- Network access and permissions

## Quick Access

- Prowlarr: `http://<SERVER_IP>:9696`
- Sonarr: `http://<SERVER_IP>:8989`
- Radarr: `http://<SERVER_IP>:7878`

## Installation Guide

1. **Update your system**

    ```bash
    sudo apt-get update -y
    ```

2. **Set up shared storage**

    - Replace `USERNAME`, `IP_OF_FILESERVER`, and `shared` with your details.

    ```bash
    sudo apt-get install cifs-utils -y
    mkdir /home/USERNAME/shared
    echo "//IP_OF_FILESERVER/shared /home/USERNAME/shared cifs username=USERNAME,noauto,rw,users 0 0" | sudo tee -a /etc/fstab
    mount -t cifs //IP_OF_FILESERVER/shared /home/USERNAME/shared -o username=USERNAME
    ```

3. **Install Docker**

    - This script installs Docker, allowing you to run applications in containers.

    ```bash
    cd /home/USERNAME/shared
    sudo curl -fsSL https://get.docker.com -o install-docker.sh
    sudo sh install-docker.sh
    ```

4. **Configure Docker and applications**

    - This section outlines the Docker commands to run Jellyfin, Sonarr, Radarr, Prowlarr, and Transmission. Replace `USERNAME`, `USERPASSWORD`, and paths as needed.

    ```bash
    # Add your user to the Docker group
    sudo groupadd docker
    sudo usermod -aG docker $(whoami)

    # Jellyfin setup
    ...

    # Sonarr setup
    ...

    # Continue with Radarr, Prowlarr, and Transmission setup as originally provided
    ```

### Additional Tips and Troubleshooting

Offer tips for common issues users might encounter and how to troubleshoot them. This section can be expanded based on user feedback.

```markdown
## Tips and Troubleshooting

- Ensure your `USERNAME` and `IP_OF_FILESERVER` are correctly replaced throughout the script.
- If you encounter permissions issues, verify that your user is correctly added to the Docker group and that you have logged out and back in for the changes to take effect.
- For networking issues, ensure your firewall settings allow traffic on the necessary ports.

## Conclusion

Congratulations on setting up your Ubuntu Media Server! You now have a powerful setup for managing and streaming your media content. Explore adding your media to Jellyfin, configuring Sonarr and Radarr for automatic downloads, and optimizing your server performance.

Feel free to contribute to this guide or suggest improvements. Happy streaming!


## Original script
```
sudo apt-get update -y
#sudo apt install -t $(grep VERSION_CODENAME /etc/os-release | cut -d '=' -f 2)-backports cockpit -y
#sudo apt-get install cockpit-machines -y
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
docker run -d --name=sonarr -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -p 8989:8989 --volume snr-config:/config --mount type=bind,source=/home/USERNAME/shared/TV,target=/tv --mount type=bind,source=/home/USERNAME/shared/torrents/downloads,target=/downloads --restart unless-stopped lscr.io/linuxserver/sonarr:latest

docker volume create prw-config
docker run -d --name prowlarr -p 9696:9696 -e PUID=1000 -e PGID=1000 -e UMASK=002 -e TZ="Etc/UTC" --volume prw-config:/config ghcr.io/hotio/prowlarr

docker volume create rdr-config
docker run -d --name=radarr -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -p 7878:7878 --volume radarr-config:/config -v /home/USERNAME/shared/Movies:/movies --restart unless-stopped lscr.io/linuxserver/radarr:latest

docker volume create transm-config
docker run -d --name=transmission -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -e USER=USERNAME -e PASS=USERPASSWORD -p 9091:9091 -p 51413:51413 -p 51413:51413/udp --volume transm-config:/config -v /home/USERNAME/shared/torrents/downloads:/downloads -v /home/USERNAME/shared/torrents/watched:/watch --restart unless-stopped lscr.io/linuxserver/transmission:latest



```
