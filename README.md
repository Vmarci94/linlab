## 1.4
A céges policy-k szerint a távoli bejelentkezést biztosı́tó szolgáltatásoknak (ssh) csak a lokális hálózatról kell elérhetőnek lenniük. Módosı́tsa a tűzfalszabályokat ennek megfelelően!
```bash
#Először is kell az ipcímed
ip addr
# megkapod pl: 172.31.37.59/28
# be kell állítani, hogy csak lokális hálózatról lehessen belépni ssh-val
#  32-28 = 4 ⇒ ezért 4 bit a végéről
#  59 ⇒ **0011 1011**
#  tehát a hálózati címem: 172.31.37.48 a boardcast pedig: 172.31.37.63
# megoldás végül:
sudo iptables -I INPUT -p tcp --dport 22 -s 172.31.37.48/28 -j ACCEPT
#ez minden mást letilt
sudo iptables -A INPUT -p tcp --dport 22 -j REJECT
# mentsd el az iptable-t
sudo /sbin/iptables-save
```
## 1.5
A céges policy-k szerint a gépnek nem szabad válaszolnia a pingelésre. Módosı́tsa ennek megfelelően a tűzfal szabályait!
```bash
sudo iptables -I INPUT -p icmp --icmp-type echo-request -j DROP
sudo /sbin/iptables-save
```
## 1.6
Adjon hozzá a rendszerhez egy új felhasználót, akinek a neve legyen "mekkelek”, jelszava pedig az Ön NEPTUN kódja! Nézze meg, milyen új bejegyzés született az /etc/passwd fájlban!
```bash
sudo adduser mekkelek
#meglesni ezzel:
cat /etc/passwd | grep mekkelek
```
## 1.7
Tegye lehetővé az új felhasználó számára, hogy rendszergazda jogokkal futtathasson minden programot!
```bash
sudo nano /etc/sudoers
# A(z) 'User privilege specification' részhez, a root alá vedd fel mekkelek-et
mekkelek ALL=(ALL:ALL) ALL
```
**elvileg itt már megvan a 40% az első feladatból**
## 1.8
A biztonság érdekében tiltsa le a root felhasználó SSH-n történő bejelentkezésének jogát!
hint ⇒ `mc , /etc/ssh/sshd config , systemctl restart ssh`
```
# NEM AZ SSH_CONFIG!!!!
bashsudo nano /etc/ssh/sshd_config
## elvileg benne van kikommentezve, szóval engedélyezd: PermitRootLogin no
sudo service sshd restart
```
## 1.9
Valósı́tson meg RSA kulcsokkal működő autentikációt a fizikai és a virtuális gép között a saját, újonnan létrehozott (mekkelek) felhasználó számára! Ehhez az ssh-keygen program segı́tségével hozzon létre egy új publikus-privát kulcspárt. A létrehozott kulcspár privát részét másolja át a gazdagépre, majd a puttygen program segı́tségével konvertálja át a PuTTY számára emészthető formátumba. Végezetül a virtuális gépen a 
`cat  ̃/.ssh/id_rsa.pub >>  ̃/.ssh/authorized_keys` 
parancs kiadásával engedélyezze Mekk Elek RSA bejelentkezését! Tesztelje le az új lehetőséget!

```bash
su - mekkelek # ha nem ott lennénk
ssh-keygen
#Aztán a feladat utasításait kell követni
```
## 1.10
Telepı́tse a MySQL-t a guest gépre! Nézze meg, hogy a szerver melyik verzióját sikerült installálnia! Állı́tsa be, hogy a rendszergazda csak a "root" jelszóval léphessen be. Ne feledje letiltani a socket alapú azonosı́tást sem!
hint ⇒ `mysql secure installation , UPDATE user ...`
```bash
sudo apt-get install mysql-server
sudo mysql secure installation
cat /etc/mysql/my.cnf | grep port
cat /etc/mysql/my.cnf | grep -i datadir
```
