# Commands

Useful Linux Commands

----------------------------------------------------------------------

## View Processs Running

```bash
top -bn 1
```

----------------------------------------------------------------------

## Rsync

- **Local**

```bash
rsync -vharP backup.tar /tmp/backups/
```

- **Remote**

```bash
rsync -azrP source uesr@host:/destination
```

----------------------------------------------------------------------

## Mount Driver

```bash
ll /dev/disk/by-uuid/ && ll /dev/disk/by-label/
```

```bash
sudo vim /etc/fstab
```

> +++
```txt
UUID=e2a5bf75-e511-4330-9f1e-efc114b9a47e /media/o/Tera  ext4    errors=remount-ro 0       1
```

----------------------------------------------------------------------

## Fix grub autodetect

```bash
sudo apt install os-prober && \
sudo os-prober && \
sudo update-grub
```

----------------------------------------------------------------------

## Convert epub to mobi.

```bash
for book in *.epub; do echo "Converting $book"; ebook-convert "$book" "$(basename "$book" .epub).mobi"; done
```

----------------------------------------------------------------------

## Cloc

Count Lines of Code [Website](https://github.com/AlDanial/cloc)

```bash
cloc .
```

```bash
cloc --exclude-lang=DTD,Lua,make,Python .
```

```bash
find . -name "*.php" | xargs cloc
```
## Git Cloc

```bash
git cloc -r
```

```bash
git cloc
```

```bash
git cloc --month 2014-6
```

----------------------------------------------------------------------

## SSH

### SSH Keygen

```bash
$ ssh-keygen -t rsa -b 4096

$ ssh-keygen -t rsa -b 4096 -f  ~/.ssh/gitlab -C "www.gitlab.com"

$ ssh-keygen -t dsa

$ ssh-keygen -t rsa

$ ssh -X user@host

$ ssh-keygen -t dsa -f  /home/o/.ssh/NAME

$ ssh-keygen -t dsa -f  /home/o/.ssh/[NAME] -C [COMENTARIO]

$ ssh-keygen -t dsa -f  /home/o/.ssh/gitlab -C "www.gitlab.com"
```

### SSH Config File

> /home/o/.ssh/config

```bash
Host *
  ServerAliveInterval 240

# |::::::: Virtual Machine Centos - Owncloud - Web
Host FRIENDLY_NAME
    HostName 192.168.77.26
    Port 22
    User root
    IdentityFile ~/.ssh/PRIV_KEY
    Compression yes
    CompressionLevel 9
    # RemoteForward 52698 127.0.0.1:52698
    # RemoteForward 52698 localhost:52698
    IdentitiesOnly yes
```


- **Authentication**

```bash
    $
    ssh -p 22 -o PubkeyAuthentication=no root@192.168.0.13
```

- **Upload ssh key**

```bash
  $
  scp ~/.ssh/id_rsa.pub user@host:~/.ssh/authorized_keys
```

----------------------------------------------------------------------


## SCP

- **Upload Download files**

```bash
  $
  scp FILE USER@SERVER:LOCATION
  scp SourceFile user@host:directory/TargetFile
```

----------------------------------------------------------------------

## Download full website

```bash
wget \
      -p \
      --recursive \
      --no-clobber \
      --page-requisites \
      --html-extension \
      --convert-links \
      --restrict-file-names=windows \
      --domains www.atlassian.com \
      --no-parent \
          www.atlassian.com/es/git/workflows
```

----------------------------------------------------------------------

## Linux Kernels

### Remove old linux kernels safety

```bash
  #  Ubuntu 16.04 and newer, Linux Mint 18 and derivatives
  sudo apt install -y byobu
  sudo purge-old-kernels
```

### Remove All Unused Linux Kernel Headers, Images and Modules

```
  $
  dpkg -l 'linux-*' | sed '/^ii/!d;/'"$(uname -r | sed "s/\(.*\)-\([^0-9]\+\)/\1/")"'/d;s/^[^ ]* [^ ]* \([^ ]*\).*/\1/;/[0-9]/!d' | xargs sudo apt-get -y purge
```

----------------------------------------------------------------------

## Firewall

### Use ufw

```bash
sudo ufw allow 53
```

### Use Iptables

- **Ubuntu**

```bash
  $ sudo /sbin/iptables -A INPUT -i eth0 -p tcp --destination-port 3306 -j ACCEPT
```

- **Centos**

```bash
  $
  vim /etc/sysconfig/iptables

  +++
  -A INPUT -m state --state NEW -m tcp -p tcp --dport 3000 -j ACCEPT

  $
  /sbin/service iptables restart
```

----------------------------------------------------------------------

## Cron


### View Root User Cronjob

```bash
crontab -l
```

### View Users Cronjob

```bash
crontab -u USER_NAME -l
```

### Install or create or edit my own cron jobs

```bash
  $
crontab -e
```

### Syntax of crontab

```bash
1 2 3 4 5 /path/to/command arg1 arg2

# or

1 2 3 4 5 /root/backup.sh

# or

1 2 3 4 5 USERNAME /path/to/command arg1 arg2

# Format

* * * * * command to be executed
- - - - -
| | | | |
| | | | ----- Day of week (0 - 7) (Sunday=0 or 7)
| | | ------- Month (1 - 12)
| | --------- Day of month (1 - 31)
| ----------- Hour (0 - 23)
------------- Minute (0 - 59)
```

### Cron examples

```bash
# Pull and log
*/1 * * * *   /root/crons/prototiposena.sh  >> /root/crons/logs/prototiposena.$(date +\%Y\%m\%d).log 2>&1
*/1 * * * *   /root/crons/time_stamp.sh  >> /root/crons/logs/prototiposena.$(date +\%Y\%m\%d).log 2>&1

*/3 * * * *   /root/crons/prototiposena_prueba.sh  >> /root/crons/logs/prototiposena_prueba.$(date +\%Y\%m\%d).log 2>&1
*/3 * * * *   /root/crons/time_stamp.sh  >> /root/crons/logs/prototiposena_prueba.$(date +\%Y\%m\%d).log 2>&1

# prototiposena_dropbox.sh
00 20 * * *    /root/crons/prototiposena_dropbox.sh  >> /root/crons/logs/prototiposena_dropbox.$(date +\%Y\%m\%d).log 2>&1
00 20 * * *    /root/crons/time_stamp.sh  >> /root/crons/logs/prototiposena_dropbox.$(date +\%Y\%m\%d).log 2>&1

# Remove file older than 7 days
0 0 * * *     find /root/crons/logs -mtime +7 -exec rm {} \;
```

----------------------------------------------------------------------

## Change Keymap

### Usando el comando `xmodmap`

- **Create File**

```bash
  $
  touch ~/.xmodmap
  vim ~/.xmodmap
```

- **Edit:**

>>> ~/.xmodmap

```bash
  +++
  !
  ! make shift-, be < and shift-. be >
  !
  keysym comma = comma semicolon less less less
  keysym period = period colon greater greater greater


  !
  ! Add <> to zx
  ! ORIGINAL
  ! keycode  52 = z Z z Z guillemotleft less guillemotleft less
  ! keycode  53 = x X x X guillemotright greater guillemotright greater
  !
  keycode  52 = z Z z Z guillemotleft less less less
  keycode  53 = x X x X guillemotright greater greater greater

```

- **Execute**

```bash
    xmodmap -verbose ~/.xmodmap
```

- **Ver teclas**

```bash
  $ xmodmap -pke | less
```

- **Ver las lista de dispositivos**

```bash
  $ xinput list
```

- **Diagnostico de las teclas**

```bash
  $ xinput query-state 9
```

----------------------------------------------------------------------

## Clear Memory Cache

```bash
sudo sh -c "sync; echo 3 > /proc/sys/vm/drop_caches"
```

----------------------------------------------------------------------

## Fix locales

```bash
export LANGUAGE=en_US.UTF-8   && \
export LANG=en_US.UTF-8       && \
export LC_ALL=en_US.UTF-8     && \
sudo locale-gen en_US.UTF-8   && \
sudo dpkg-reconfigure locales

export LANGUAGE=es_ES.UTF-8   && \
export LANG=es_ES.UTF-8       && \
export LC_ALL=es_ES.UTF-8     && \
sudo locale-gen es_ES.UTF-8   && \
sudo dpkg-reconfigure locales
```



----------------------------------------------------------------------
