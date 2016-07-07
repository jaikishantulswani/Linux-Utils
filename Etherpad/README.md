

sudo useradd --create-home etherpad
sudo passwd etherpad
su - etherpad
cd /home/etherpad
git clone git://github.com/ether/etherpad-lite.git


mysql -u root -p
create database `etherpad-lite`;
grant all privileges on `etherpad-lite`.* to 'etherpad'@'localhost' identified by 'pistacho0';
FLUSH PRIVILEGES;
exit

cd /home/etherpad/etherpad-lite 
mkdir logs
cp settings.json.template settings.json

vim settings.json

```json
---
    "dbType" : "dirty",
  //the database specific settings
    "dbSettings" : {
                    "filename" : "var/dirty.db"
                },
+++
   // An Example of MySQL Configuration
   "dbType" : "mysql",
   "dbSettings" : {
                    "user"    : "etherpad",
                    "host"    : "localhost",
                    "password": "pistacho0",
                    "database": "etherpad-lite"
                  },
```

./bin/installDeps.sh
./bin/run.sh

Ctrl+C 

mysql -u root -p
alter database `etherpad-lite` character set utf8 collate utf8_bin;
use `etherpad-lite`;
alter table `store` convert to character set utf8 collate utf8_bin;
exit

chmod +x /etc/init.d/etherpad-lite

sudo ln -s /home/etherpad/etherpad-lite /usr/share/etherpad-lite

sudo vim /etc/init.d/etherpad-lite

```bash
#!/bin/sh

### BEGIN INIT INFO
# Provides:          etherpad-lite
# Required-Start:    $local_fs $remote_fs $network $syslog
# Required-Stop:     $local_fs $remote_fs $network $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: starts etherpad lite
# Description:       starts etherpad lite using start-stop-daemon
### END INIT INFO

PATH="/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/opt/node/bin"
LOGFILE="/home/etherpad/etherpad-lite/logs/etherpad-lite.log" 
EPLITE_DIR="/home/etherpad/etherpad-lite"
EPLITE_BIN="bin/safeRun.sh"
USER="etherpad"
GROUP="etherpad"
DESC="Etherpad Lite"
NAME="etherpad-lite"

set -e

. /lib/lsb/init-functions

start() {
  echo "Starting $DESC... "

    start-stop-daemon --start --chuid "$USER:$GROUP" --background --make-pidfile --pidfile /var/run/$NAME.pid --exec $EPLITE_DIR/$EPLITE_BIN -- $LOGFILE || true
  echo "done"
}

#We need this function to ensure the whole process tree will be killed
killtree() {
    local _pid=$1
    local _sig=${2-TERM}
    for _child in $(ps -o pid --no-headers --ppid ${_pid}); do
        killtree ${_child} ${_sig}
    done
    kill -${_sig} ${_pid}
}

stop() {
  echo "Stopping $DESC... "
  if test -f /var/run/$NAME.pid; then
    while test -d /proc/$(cat /var/run/$NAME.pid); do
      killtree $(cat /var/run/$NAME.pid) 15
      sleep 0.5
    done
    rm /var/run/$NAME.pid
  fi
  echo "done"
}

status() {
  status_of_proc -p /var/run/$NAME.pid "" "etherpad-lite" && exit 0 || exit $?
}

case "$1" in
  start)
      start
      ;;
  stop)
    stop
      ;;
  restart)
      stop
      start
      ;;
  status)
      status
      ;;
  *)
      echo "Usage: $NAME {start|stop|restart|status}" >&2
      exit 1
      ;;
esac

exit 0
```