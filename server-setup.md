# Server setup
## Prerequisites
- A running server with [Ubuntu 22.04 LTS](https://releases.ubuntu.com/) or above.
- Credentials to log in as the root user on the server.
- An [SSH client](https://www.ssh.com/academy/ssh/client) on your local machine.

## Login over SSH and create a user with root privileges
Log in to your server as root user. If this is the first time connecting to your server over SSH, this will warn you and ask if you trust the server, answer `yes` to confirm. Then it should ask you for the password of the root user on the server.

On your local machine:
```
ssh root@<HOST_ADDRESS>
```

Allowing to log in as root to your server is a security threat. To mitigate this threat, add a user and add it to the sudo group to give it root privileges. This allows you to log in to your server as that user, without giving immediate root access to your server.

On your server:
```
adduser <USER> && adduser <USER> sudo
```

This will ask you to set a password for the user. Now you should be able to log into your newly created user over SSH.

On your local machine:
```
ssh <USER>@<HOST_ADDRESS>
```

## Connect over SSH using keys
Using password based credentials to log in is considered unsafe, passwords can be guessed and are often reused elsewhere. To mitigate this threat and allow automatic authentication, we will create an [SSH key pair](https://www.ssh.com/academy/ssh/key) for your local user and use them to authenticate as the remote user we just created on the server.

First create the key pair on your local machine. By default this will create a private and public key file in the `.ssh` directory in the home directory of your user. At some point it will ask you if you want to set [a passphrase](https://www.ssh.com/academy/ssh/passphrase), this will encrypt the keys on your local machine as an optional safety measure.

On your local machine:
```
ssh-keygen -t ed25519
```

To be able to authenticate as the remote user using the private key of the local user, the public key of the local user must be added to the authorized keys of the remote user. The following command will create an `.ssh` directory and `.ssh/authorized_keys` file in the home directory of the remote user and adds the public key of your local user to the file.

On your local machine:
```
ssh-copy-id -p <PORT> <USER>@<HOST_ADDRESS>
```

If that command fails, you can use the following command to copy the public key over ssh manually.

```
cat ~/.ssh/id_ed25519.pub | ssh <USER>@<HOST_ADDRESS> "mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

You can now disable password authentication and root access in the SSH configuration file.

On your server:
```
sudo nano /etc/ssh/sshd_config
```

Then find and set the following parameters.

```
...
PasswordAuthentication no
...
PermitRootLogin no
...
```

Restart the SSH service to commit the changes.

On your server:
```
sudo systemctl restart ssh.service
```

Now you should be able to log into your server as the remote user, without having to provide the credentials. You'll have to provide the passphrase if you chose to set it when creating the keys.

On your local machine:
```
ssh <USER>@<HOST_ADDRESS>
```

## Change the SSH port
As an extra safety measure you can set the SSH port to [a random port between 1024 and 65535](https://www.google.com/search?q=random+number+between+1024+and+65535) (Check the [list of ports used by protocols](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers) to be sure you are not blocking a protocol you use). To do this, once again open the SSH configuration file.

On your server:
```
sudo nano /etc/ssh/sshd_config
```

Then find and set the following parameter.

```
...
Port <PORT>
...
```

Restart the SSH service to commit the changes.

On your server:
```
sudo systemctl restart ssh.service
```

Be sure to use this port when accessing your server over SSH.

On your local machine:
```
ssh -p<PORT> <USER>@<HOST_ADDRESS>
```

## Change the name of the server
Change the name and host of your server by editing the host files:

```
sudo nano /etc/hostname
```

```
sudo nano /etc/hosts
```

Reboot to commit the changes.

```
sudo reboot
```

## Enable a firewall
The default firewall for Ubuntu is `ufw`, be sure to allow your SSH port before enabling the firewall, to not lock yourself out:

```
sudo ufw allow <PORT>
```

Then enable the firewall:

```
sudo ufw enable
```