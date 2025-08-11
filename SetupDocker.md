## Download Docker
use this command to get the official docker download
```
curl -sSL https://get.docker.com | sh
```

## Setup Permissions
now add yourself to the docker group
```
sudo usermod -aG docker $USER
```

now to apply changes logout and log back in 
```
logout
login
```

check group
```
groups
```
