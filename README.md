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

- cp4 yum  
check whether there are any updates:
```
yum check-update
```
install yum-cron to customize this.  

search:
```
yum search x
```
get info:
```
yum info x
```
get dependencies:
```
yum deplist x
```
remove:
```
yum remove x y z
```
clean cached packages
```
yum clean packages
yum clean metadata
yum clean dbcache
```
These commands are equi to
```
yum clean all
```
To rebuild cache, use
```
yum makecache
yum -y update --skip-broken
```

install priority plugin:

```
yum install yum-plugin-priorities
```
open config file
```
vi /etc/yum/pluginconf.d/priorities.conf
```
should say enabled=1.
Then 
```
vi /etc/yum.repos.d/CentOS-Base.repo
```
Add
```
priority=1
```
in [base],[updates],[extras]section.


Third party repos
```
cd /etc/yum.repos.d
```
change appropriate repo from enabled=0 to enabled=1


create a yum repo

using  rpm
```
rpm -Uvh
rpm -qa | grep ""
```

remove:
```
rpm -e x
```

query:
```
rpm -qi x (information)
rpm -ql x (full filename)
rpm -qd x (doc)
```

import GPG key:
```
rpm --import ....
```

get information of a local rpm file:
```
rpm -qip x
```
show all files:
```
rpm -qlp x
```
install using yum using localinstall
```
wget x.rpm
yum localinstall x.rpm
```





- cp8 FTP
```
yum install vsftpd
```


- cp10 databases
install mariadb
```
yum install mariadb mariadb-server
systemctl enable/start mariadb.server
mysql_secure_installation
```
create a database
```
CREATE DATABASE name CHARACTER SET utf8 COLLATE utf8-general-ci
```

allow remote access
```
yum install firewalld
firewall-cmd --permanent --add-service=mysql &&firewall-cmd --reload
```
grant access to a remote user:
```
GRANT SELECT ON mysql.user TO 'user@remoteip' IDENTIFIED BY '';FLUSH PREVILEGES;EXIT;
```
Connect from remote:
```
echo "show databases" | mysql -u x -p mysql -h hostip
```

