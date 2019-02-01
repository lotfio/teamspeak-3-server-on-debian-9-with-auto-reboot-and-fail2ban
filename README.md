# Create a ts3 user 
 
```php
  sudo adduser --disabled-login ts3
```
 
### Get the latest TeamSpeak 3 server files for 64-bit Linux.

```php
  // lets cd to the location where we want to install our ts server
  cd /opt
  wget http://dl.4players.de/ts/releases/3.1.1/teamspeak3-server_linux_amd64-3.2.3.tar.bz2
```
  
 ### Extract the archive.
  ```php
    sudo tar -xvf teamspeak3-server_linux_amd64-3.1.1.tar.bz2
  ```
  
### In the extracted folder, we have the conditions for using Teamspeak servers. It will be necessary to accept them by creating a file before starting the server.
  ```php
      cd teamspeak3-server_linux_amd64
      #Reading the Terms
      nano LICENSE
      # Creation of the acceptance file
      touch .ts3server_license_accepted
  ```

### We will now start our server for the first time. At the first start of the server, we will have access to very important information, which will allow you to administer your server. It will therefore be necessary to note the connection information of the Admin Server and the Token.
```php
    sudo sh ts3server_startscript.sh start

    ------------------------------------------------------------------
                          I M P O R T A N T                           
    ------------------------------------------------------------------
                   Server Query Admin Account created                 
             loginname= "serveradmin", password= "xxxxxxxx"
    ------------------------------------------------------------------

    ------------------------------------------------------------------
                          I M P O R T A N T                           
    ------------------------------------------------------------------
          ServerAdmin privilege key created, please use it to gain 
          serveradmin rights for your virtualserver. please
          also check the doc/privilegekey_guide.txt for details.

           token=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ------------------------------------------------------------------
 ```
## Make the TeamSpeak 3 server start on boot. Use your favorite editor to make a new file called teamspeak in /etc/init.d/.
```php
  nano /etc/init.d/ts3
```
## Populate it with this content.

```php
  #!/bin/sh
  ### BEGIN INIT INFO
  # Provides:         teamspeak
  # Required-Start:   $local_fs $network
  # Required-Stop:    $local_fs $network
  # Default-Start:    2 3 4 5
  # Default-Stop:     0 1 6
  # Description:      Teamspeak 3 Server
  ### END INIT INFO

  ######################################
  # Customize values for your needs: "User"; "DIR"

  USER="ts3"
  DIR="/opt/teamspeak3-server_linux_amd64"

  ###### Teamspeak 3 server start/stop script ######

  case "$1" in
  start)
  su $USER -c "${DIR}/ts3server_startscript.sh start"
  ;;
  stop)
  su $USER -c "${DIR}/ts3server_startscript.sh stop"
  ;;
  restart)
  su $USER -c "${DIR}/ts3server_startscript.sh restart"
  ;;
  status)
  su $USER -c "${DIR}/ts3server_startscript.sh status"
  ;;
  *)
  echo "Usage: {start|stop|restart|status}" >&2
  exit 1
  ;;
  esac
  exit 0
 ```
 
 ### Once you are done, save the file and close the editor.
 ### Make it executable and add it to the service.
 ```php
  chmod +x /etc/init.d/ts3
  update-rc.d ts3 defaults
 ```
 ### Make sure to change teamspeak folder ownership to ts3 user 
 ```php
 sudo chown -R ts3:ts3 /opt/teamspeak3-server_linux_amd64
 ```
 
 # Fail2ban configuration
  *create a teamspeak.conf filter on your /etc/fail2ban/filter.d*
  ```php
    sudo nano /etc/fail2ban/filter.d/teamspeak.conf
    
    // populate it with this content
    [INCLUDES]
    before = common.conf
    [Definition]
    failregex = ^(.*)query from [0-9]{1,} :[0-9]{1,5} attempted to login with account "(.*)" and failed!$
    ignoreregex =
  ```
  *Make sure to enable all logs on your teamspeak server configuration*
  
  ## Now add your filter to the jail.local fail

  ```php
     sudo nano /etc/fail2ban/jail.local
     
     add this rules to your file
     
     [teamspeak]
     enabled  = true
     port     = 9987,10011,30033
     filter   = teamspeak
     logpath  = /opt/ts3/teamspeak3-server_linux_amd64/logs/ts3server_*.log
     maxretry = 3
     bantime  = 86400
     findtime = 7800
  ```
  ## Now check if our teamspeak filter is working by running the following commands:
  ```php
   // this command will list all the enabled filters
   sudo fail2ban-client status 
   
   // this command will load the status of teamspeak filter and additional details
   sudo fail2ban-client status teamspeak
  ```

