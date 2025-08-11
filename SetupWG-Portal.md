## Setup Compose file

First make the directory to store wg-portal's various configurations
```
sudo mkdir -p /opt/stacks/wg-portal
cd /opt/stacks/wg-portal
```

Now make the compose file and the config and data directories that the wg-portal container will use
```
sudo touch compose.yml
sudo mkdir config
sudo mkdir data
```

Using your favorite text editor edit the file and paste this:
```
services:
  wg-portal:
    image: wgportal/wg-portal:2.0.0
    container_name: wg-portal
    restart: unless-stopped
    logging:
      options:
        max-size: "10m"
        max-file: "3"
    cap_add:
      - NET_ADMIN
    # Use host network mode for WireGuard and the UI. Ensure that access to the UI is properly secured.
    network_mode: "host"
    volumes:
      # left side is the host path, right side is the container path
      - /etc/wireguard:/etc/wireguard
      - ./data:/app/data
      - ./config:/app/config
```
  
Now change directories to the config directory you just created and create a new config file

```
cd config
sudo touch config.yml
```

Now, there are a lot of things you could play around with here to tweak various settings but we will add

```
web:
  external_url: http://<IP ADDRESS>:8888
```
This is the address that will be used to access the portal when not on a host machine. If you have set up a reverse proxy you can set up a URL to point to this service and you would put that URL there instead.
Other settings are available at: https://wgportal.org/master/documentation/configuration/overview/


Now go back to the parent directory and start the container with
```
docker compose up -d
```

## Things to consider
- You should now be able to access the wireguard portal and configure peers on the portal for your vpn. If you have a DDNS you should set the default endpoint to be your sub.domain.com:51820.  
- Additionally in the Allowed IPs section of the peer setup use 0.0.0.0/0 if you want all traffic to be routed through the tunnel otherwise specify the IPs of what you would like to be routed  
- Lastly in the DNS Server section of the peer setup use your server's ip if using something like pihole otherwise choose another secure dns such as 1.1.1.1 (cloudflare)
- It is also reccomended that you use a firewall to block access to the port 8888 from all IP's you do not want the page viewable at. Also it is just good security practice to have all unused ports blocked with a firewall such as UFW. 
