WebLogic startup on server boot.
################################
:date: 2010-11-19 03:33
:author: Laurence Noton
:category: Script

To have weblogic start up when the linux server boots you must create
rc.d scripts.  If your environment is clustered it will be as simple as
starting the admin server (if required) and then the node manager.

Admin server script:

#!/bin/bash

#

# Startup script for Weblogic Node Manager

#

# chkconfig: - 86 15

# description: WebLogic Admin Server

# processname: admin server

JAVA\_HOME=/apps/Oracle/Middleware/jdk160\_14\_R27.6.5-32

export JAVA\_HOME

JAVA\_VENDOR=Sun

export JAVA\_VENDOR

fp\_domain=/apps/Oracle/Middleware/user\_projects/domains/fp\_domain

start\_adminserver=startWebLogic.sh

start\_adminserver() {

echo -n "Starting weblogic fp\_domain admin server: "

su weblogic -c "nohup $fp\_domain/startWebLogic.sh >
/home/weblogic/adminserver.out 2>&1 &"

echo "done."

}

# See how we were called

case "$1" in

start)

start\_adminserver

;;

\*)

echo "Usage: $0 {start}"

exit 1

esac

exit 0

 

Node manager script:

#!/bin/bash

#

# Startup script for Weblogic Node Manager

#

# chkconfig: - 86 15

# description: WebLogic Node Manager

# processname: nodemanager

JAVA\_HOME=/apps/Oracle/Middleware/jdk160\_14\_R27.6.5-32

export JAVA\_HOME

JAVA\_VENDOR=Sun

export JAVA\_VENDOR

nm\_home=/apps/Oracle/Middleware/wlserver\_10.3/server/bin

start\_nodemanager=startNodeManager.sh

start\_nodemanager() {

echo -n "Starting weblogic node manager: "

su weblogic -c "nohup $nm\_home/startNodeManager.sh >
/home/weblogic/nodemanager.out 2>&1 &"

echo "done."

return 0

}

# See how we were called

case "$1" in

start)

start\_nodemanager

;;

\*)

echo "Usage: $0 {start}"

exit 1

esac

exit 0

 
