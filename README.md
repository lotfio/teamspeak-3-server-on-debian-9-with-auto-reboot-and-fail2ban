# Create a ts3 user 
 
```python
  sudo adduser --disabled-login ts3
```
 
# Get the latest TeamSpeak 3 server files for 64-bit Linux.

```python
  wget http://dl.4players.de/ts/releases/3.1.1/teamspeak3-server_linux_amd64-3.2.3.tar.bz2
```
  
 # Extract the archive.
  ```python
    cd /opt
    sudo tar -xvf teamspeak3-server_linux_amd64-3.1.1.tar.bz2
  ```
  
 # In the extracted folder, we have the conditions for using Teamspeak servers. It will be necessary to accept them by creating a file before starting the server.
  ```python
      cd teamspeak3-server_linux_amd64
      #Reading the Terms
      nano LICENSE
      # Creation of the acceptance file
      touch .ts3server_license_accepted
  ```

# We will now start our server for the first time. At the first start of the server, we will have access to very important information, which will allow you to administer your server. It will therefore be necessary to note the connection information of the Admin Server and the Token.
```
    sh ts3server_startscript.sh start

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
#Make the TeamSpeak 3 server start on boot. Use your favorite editor to make a new file called teamspeak in /etc/init.d/.
```python
  nano /etc/init.d/teamspeak
```
# Populate it with this content.

```
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

  USER="teamspeak"
  DIR="/opt/teamspeak3/server"

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
 
 # Once you are done, save the file and close the editor.
 # Make it executable and add it to the service.
 ```python
  chmod +x /etc/init.d/teamspeak
  update-rc.d teamspeak defaults
 ```
