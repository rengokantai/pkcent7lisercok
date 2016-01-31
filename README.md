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





- cp8 FTP
```
yum install vsftpd
```
