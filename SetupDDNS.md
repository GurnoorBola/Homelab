## Setup Dynu DDNS
go to https://www.dynu.com/en-US/ and sign up for an account

Create a hostname in the DDNS tab of the site

## Server setup
Now switch to your server device

We will set up a script to update the ip stored by Dynu to make sure it is accurate should your ip change

First make a director to store the script and make the script file
```
cd ~
sudo mkdir dynudns
cd dynudns
sudo touch dynu.sh
```

Generate a md5 hash of your Dynu password 
```
echo -n <password> | md5sum
```

Now, using your favorite text editor, edit the file and add this replacing USERNAME with your username and PASSWORD with the md5 hash of your password
```
echo url="https://api.dynu.com/nic/update?username=USERNAME&password=PASSWORD" | curl -k -o ~/dynudns/dynu.log -K - 
```

Make the file executable
```
chmod 700 dynu.sh
```

Now add this to the bottom of your crontab to make the script run every 5 minutes
```
crontab -e
*/5 * * * * ~/dynudns/dynu.sh >/dev/null 2>&1 
```

To use this domain with let's encrypt for a SSL use your proxy manager and insert API key where it asks for it when trying to create a certificate.

## Next Steps
Now you can use this dynamic dns to route to your server.  
- For example in a wireguard client config you could use sub.domain.com:51820 as the destination for your traffic. 
