#### pkcent7lisercok
- cp1
```
yum -y install "Base" "Development Libraries" "Development Tools"
yum -y install policycoreutils-python
```
`
configure GRUB

```
vi /etc/default/grub
dmesg | grep -Fq "EFI v"
```
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```

- cp2
```
localectl status
localectl list-locales
localectl list-keymaps
localectl set-locale LANG=de_DE.utf8
```
others:
```
yum install man-pages
export LANG="en_US.utf-8
```
permanent:
```
echo "export LANG='de_de.utf-8'" >> ~/.bashrc
```

update clock:
```
yum install chrony
systemctl start chronyd
systemctl enable chronyd(permanent use)
```

check
```
timedatectl
timedatectl set-ntp yes
```

the config file is
```
vi /etc/chrony.conf
```

checking whether is working
```
chronyc tracking
chronyc sources
```
some utils
```
timedatectl list-timezone
timedatectl set-timezone Europe/Berlin
```
sync with hardware clock:
```
hwclock --systohc (= hwclock -w)
```
config hostname
```
hostnamectl set-hostname xxx
```
find ip
```
ip addr list
```

Note host file and DNS file
```
/etc/host
/etc/resolv.conf
```

show hostname:
```
hostname
hostname --long (--fqdn)
```

build static network connection:  
network card:
```
/etc/sysconfig/network-scripts/ifcfgxx
```

set
```
NM_CONTROLLED="no" //disable
BOOTPROTO=none //disable dynamic
DEFROUTE=yes
PEERDNS=no
PEERROUTES=yes
IPV4_FAILURE-FATAL=yes
```

add a user to superuser
```
usermod -G wheel xxx
```

customize banner(before log in)
```
/etc/motd
```
customize ssh banner:
```
vi /etc/ssh/sshd_config
```
Add following at the end of the file
```
Banner /etc/ssh-banner
```
Then
```
touch /etc/ssh-banner
```
Add custom message.  
Finally, start service
```
systemctl restart sshd.service
```
However, motd can not generate dynamic messages. To generate, create a script in /etc/profile.d
First,
```
touch /etc/profile.d/motd.sh
```
Then add content:
```
#!/bin/bash
echo -e "
#######
# welcome to `hostname`, you are `whoami`
# kernel is `uname -i`
# 
"
```
check kernel
```
lsmod
modinfo ext4
```
load
```
modprobe xxx
```
uninstall
```
modprobe -r -v xxx
```
- cp3

list all running services:
```
systemctl -t service -a --state running
```
All the ```systemd``` unit files installed by the basic system during installation are in 
```
/usr/lib/systemd/system
```
others come from installing are
```
/etc/systemd/system
```

To avoid server interupt, we can use
```
systemctl reload httpd.service
```
To check which service have reload option
```
grep -l "ExecReload" /usr/lib/systemd/system/*.service /etc/systemd/system/*.service
```


print a longer version of error message
```
systemctl status httpd.service -l
```

Tracking system resources with journald
```
journalctl --since "2015-07-20 5:00:00" --until "2015-07-20 8:00:00"
```
to some service
```
journalctl -u sshd.service --since "yesterday"
```
error type
```
journalctl -p err -b
journalctl -p err -b -o verbose
journalctl -f (log output)
```
(journald is not a full replacement of rsyslog)

make journald persistent
```
mkdir /var/log/journal
systemd-tmpfiles --create --prefix /var/log/journal
systemctl restart systemd-journald
journalctl --boot=-1
```
show identity:
```
id root
```

cron file can be found at
```
/var/spool/cron/username
```
to remove a crontab:
```
crontab -r
```

cronjob folders:
```
/etc/cron.daily
/etc/cron.hourly
/etc/cron.weekly
/etc/cron.monthly
```

rsync  
Ex:  
```
mkdir ~/newfolder
rsync -avz --delete /etc/sysconfig/network-scripts/ ~/newfolder
```
--delete: delete files on the target that do not exist in the source
Then check the diff:
```
diff -r /etc/sysconfig/network-scripts/ ~/newfolder
```
should not return anything.  

exclude some type of files:(-h = human readable)
```
rsync -avzh --delete --exclude="*.zip" --exclude="*.iso" /etc/sysconfig/network-scripts/ ~/newfolder
```

rsync + sendmail:
```
#!/bin/bash
SBJT="`hostname -s`"
FROM=root@aomain
EMAIL=x@gmail.com
SOURCE=/root
DEST=/backup
LFPATH=/tmp
LF=$LFPATH/$(data +%Y%m%d_%T)_log.log
rsync --delete -log-file=$LF -avzq $SOURCE $DEST
(echo "$SBJT"; echo;cat$LF) | sendmail -f $FROM -t $EMAIL
```




- cp8 FTP
```
yum install vsftpd
```
