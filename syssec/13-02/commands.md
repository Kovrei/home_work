## Решение 1
```
 sudo apt install ecryptfs-utils
```
```
sudo adduser --encrypt-home cryptouser
```
Проверка шифрования:  
```
su - cryptouseruser
touch 123, 456
exit
```
Проверка:  
```
sudo ls /home/user2/
```

## Решение 2
Подготовка раздела (luksFormat):  
```
sudo cryptsetup -y -v --type luks2 luksFormat /dev/sdb1
```
Монтирование раздела:
```
sudo cryptsetup luksOpen /dev/sdb1 disk
ls /dev/mapper/disk
```
Форматирование раздела:  
```
sudo dd if=/dev/zero of=/dev/mapper/disk
sudo mkfs.ext4 /dev/mapper/disk
```
Монтирование «открытого» раздела:  
```
mkdir .secret
sudo mount /dev/mapper/disk .secret/
```
Завершение работы:  
```
sudo umount .secret
sudo cryptsetup luksClose disk
```
Проверка:  
```
sudo cryptsetup luksClose disk
```
## Решение 3
```
sudo apt install apparmor-profiles apparmor-utils apparmor-profiles-extra
reboot
sudo apparmor_status
```
Пример:  
```
ls /etc/apparmor.d/
sudo cp /usr/bin/man /usr/bin/man1
sudo cp /bin/ping /usr/bin/man
sudo man 127.0.0.1
sudo aa-enforce man
sudo man 127.0.0.1
```
```
