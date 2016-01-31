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

- cp8 FTP
```
yum install vsftpd
```
