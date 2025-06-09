---
title: Setting up FTP on a Linux machine
date: 2021-03-21T13:00:00+05:30
draft: false
tags:
  - self-hosted
categories:
  - self-hosted
---
There is always going to be a need to setup a good old FTP user account that your clients can then use to “optimize” their sites or to feel liberated enough to “take care of their sites on their own”. It is thus very important to safely setup an FTP server and create FTP accounts with just the right amount of permissions to keep the clients happy!!

Because nothing matters more than making a client feel liberated and happy. Even if it means spending an hour to setup something that easily has more modern and better alternatives!

![](https://media.giphy.com/media/W2zkTjEn4kv9BTeNdF/giphy.gif)

## 1. Make sure your server understands what is happening

You cannot assume that your server knows what to do when someone sends a request to port 21 with a username, password and a lot of baggage to push through when it answers the door. You need to prep that poor thing.

![](https://media.giphy.com/media/l41lRuzxPdeV6LhF6/giphy.gif)

I’ve looked around and found that `vsftpd` is the best option out there, if you need an FTP server. So lets go ahead and install that

```
$ sudo dnf install vsftpd
```

Once it is installed, you need to change some configurations because … Linux….

```
$ sudo vi /etc/vsftpd/vsftpd.conf
```

Uncomment the following two lines to allow local users to login and use FTP

```
local_enable=YES write_enable=YES
```

Next, give the guests their own room i.e. allow access to their home directories by adding the following lines in the same conf file

```
chroot_local_user=YES 
user_sub_token=$USER 
local_root=/home/$USER/ftp 
userlist_enable=YES
```

Make pam service as vsftpd to avoid a `530 login error` while accessing, by adding the following line to the sane conf file. This also needs a follow up edit in another file that we will come back to later.

```
pam_service_name=vsftpd
```

Next, to avoid a `500 OOPS: vsftpd: refusing to run with writable root inside chroot` error, add the following line too in the same conf file.

```
allow_writeable_chroot=YES
```

Then make sure to set `listen=YES` and `listen_ipv6=NO` (these directives will be present almost at the end of the same conf file)

In the end, enable `passv` mode which reminds me what it actually means but then I immediately keep forgetting so imma ask you to just copy these lines and put them in that conf file for me .. please…

![](https://media.giphy.com/media/10JLfyir1DEesM/giphy.gif)

```
pasv_enable=YES pasv_min_port=10000 pasv_max_port=10010 seccomp_sandbox=NO
```

Save the conf file (`esc + :wq` on vi) and the restart the vsftpd service

```
sudo systemctl restart vsftpd
```

To revisit the “one more step” needed to avoid `530 login error`,

```
$ sudo vi /etc/pam.d/vsftpd
```

and **comment** the following line

```
auth required pam_shells.so
```

## 2. Make sure your security guy lets people in

If you are using a cloud provider / any external firewall, please make sure there are right rules in place to allow incoming traffic on the PASSV ports (ports 10000 – 10010 in our case).

## 3. Finally… the Christening

### First time

Create a dedicate group on your server for ftp users. This will make your life easier later – like when you want to shout “all of y’all are the best!” instead of shouting at them by name.

![](https://media.giphy.com/media/13aSSyJaI5NkTm/giphy.gif)

```
groupadd ftpusers
```

### Every-time

Make sure you add your users to the `ftpusers` group when you create them. Replace `<awesome-user>` with the actual username wherever you see it.

```
$ sudo useradd <awesome_user> -g ftpusers -s /bin/false
$ sudo passwd <awesome_user>
```

Make sure you create a dedicated `ftp` folder in their home because that is going to be their default ftp location based on our configuration (`local_root` param in vsftpd.conf file above)

```
sudo mkdir /home/<awesome-user>/ftp
sudo chown <awesome-user>:ftpusers /home/<awesome-user>/ftp
sudo chmod 755 -R /home/<awesome-user>/ftp
```

…. And you’re done.

![](https://media.giphy.com/media/l0MYt5jPR6QX5pnqM/giphy.gif)