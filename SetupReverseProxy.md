## Setup Nginx Reverse Proxy Manager Config

First make the directory where we will store container files
```
sudo mkdir -p /opt/stacks/nginxproxymanager
cd /opt/stacks/nginxproxymanager
```

Now make a compose file for the container
```
sudo touch compose.yml
```

Using your favorite text editor, inside the newly created file, paste this:
```
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      # - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```

now start the container with:
```
docker compose up -d
```

if you ever want to stop the container run:
```
docker compose down
```

## Next Steps
- using your DDNS service get a valid lets encrypt certificate in the certificates tab of Nginx Proxy Manager
- you can now use this reverse proxy to create nice urls for your various services on the server. Just open the interface, write the target ip address and port and the subdomain and its done!
- in order to use this on local domains be sure to use somehting like pihole to route that domains traffic to an internal ip!!
