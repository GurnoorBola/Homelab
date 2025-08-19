## Install WireGuard

```
sudo apt install wireguard
```

## Generate Keys
Use this to generate keys
```
wg genkey | tee privatekey | wg pubkey > publickey
```

## Setup config file
We will only set up the server machine's config file since we will be using wg-portal to manage peers

make a directory to store wireguard config files

```
sudo mkdir /etc/wireguard
cd /etc/wireguard
```

then make a file called <interface name>.conf
example:
```
sudo touch wg0.conf
```

now use your favorite text editor and add this to the file filling in the keys with the keys you generated earlier.

```
[Interface]
Address = 10.8.0.1/24
PrivateKey = <server's privatekey>
ListenPort = 51820
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o wlan0 -j MASQUERADE
SaveConfig = true

```

This sets the vpn ip of the server to be 10.8.0.1 and listens on port 51820 for incoming connections  
PostUp and PostDown create and remove certain iptables rules on the starting and removal of the interface: 
- `iptables -A FORWARD -i %i -j ACCEPT` allows VPN traffic to pass into the WireGuard interface and be forwarded to its destination.
- `iptables -A FORWARD -o %i -j ACCEPT` allows VPN traffic to leave through the WireGuard interface and be forwarded to its destination.
- `iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE` ensures that packets have the correct source IP address in order to ensure packets return to the server instead of trying to reach the internal WireGuard IP.


## Router setup
Now port forward port 51820 on your router to allow vpn traffic that reaches your home network to be forwarded to the server. Steps vary per router


## How to use
you need to enable ip forwarding if not already enabled
```
sudo nano /etc/sysctl.conf

# Add/uncomment this:
# net.ipv4.ip_forward = 1
```

to make wireguard interface auto start on boot
```
sudo systemctl enable --now wg-quick@wg0
```
to remove that just replace enable with disable

to start the interface run
```
sudo wg-quick up wg0
```

similarly to remove the interface run
```
sudo wg-quick down wg0
```

Assuming you have set everything up correct you will now need to do a few more things to get things up and running. 
- set up a ddns
- set up wg-portal for easy management
