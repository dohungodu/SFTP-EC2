##Intro
This installation note created by Hung Do - Old Dominion University

Email me if you have any questions

Email: ```hdo``` at ```cs``` dot ```odu``` dot ```edu```


##Create an instance
1. Select instance type
2. Notice about the zone of instance
3. Add public IP
4. Attached public IP to instance
5. Add new data volume (50G)

See the link below for more information:
http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html
Notice: if you want to add a volume with data, the procedure will be slighly different

##PGP (Pretty Good Privacy)
1. Upload public key if you have one.
2. Create key pairs and save the private key in a safe place.

##Connect to the server 
1. Macbook or unix

- Remember chmod for the file_key
```
chmod 400 "private_key.pem"
```

- Run this script
```
ssh -i "private_key.pem" ec2-user@public_ip
```
##To chroot an SFTP directory, you must

Create an user and force root to be owner of it
```
cd /home
mkdir john
useradd -d /home/john -M -N -g users john
sudo chown root:root /home/john
sudo chmod 755 /home/john
```
Change the subsystem location on /etc/ssh/sshd_config:
```
#Subsystem sftp /usr/lib/openssh/sftp-server
Subsystem sftp internal-sftp
```
and create a user section at the end of the file (ssh can die respawning if placed after Subsystem line):
```
Match User john
ChrootDirectory /home/john
ForceCommand internal-sftp
AllowTCPForwarding no
X11Forwarding no
```

2. By windows using Putty or SecureShell (those can support connect ssh with PGP keys) 

3. Note: putty will use RSA key so need to convert .pem key to .ppk type and save public key

4. Link for reference:
https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2

##update server
```
sudo yum update
```
##Local instalation (putty setup)
http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html

##Passive mode installation:
https://www.gosquared.com/blog/fix-ftp-passive-mode-problems-on-amazon-ec2-instances

##Add new sftp group
```
sudo useradd sftp-user
sudo groupadd sftp
sudo usermod -G sftp sftp-user
sudo usermod -d /data sftp-user
```

##Add new sftp user
http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-users.html
```
sudo adduser lien
sudo su - lien
mkdir .ssh
chmod 700 .ssh
touch .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
vi .ssh/authorized_keys 
sudo usermod -G sftponly lien
```
##Change user policy
```
sudo vi /etc/ssh/sshd_config
```
###For a group
```
 Match Group sftponly
   ChrootDirectory /data
   ForceCommand internal-sftp
   AllowTcpForwarding no
   PermitTunnel no
   X11Forwarding no
```
###For a user
```
Match User username
   ChrootDirectory %h
   ForceCommand internal-sftp
   AllowTcpForwarding no
   PermitTunnel no
   X11Forwarding no
```

###Restart ssh service
```
sudo service sshd restart
```

Check owner of ```/data``` folder

Check owner of ``/data/upload``` folder

Create local key pairs and update to server the public key
https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2

