Installing Tomcat
#################
:date: 2009-03-28 13:44
:author: Laurence Noton
:category: Development

Many times I see very basic installations of Tomcat. People unzip the
download from the Apache site and that's it. They have 'installed'
Tomcat. But really this leads to quite a painful upgrade path for all
your running servers, takes up more space and additionally makes it
harder to manage rolling out common changes such as database drivers
etc. Of course there are always times when a server might need to be
up-revved when other can't leading you to having multiple installations.
The following way is the right way to install and manage Tomcat and
supports having multiple versions centrally managed.

Steps

Unzip Tomcat to a directory such as /apps/tomcat/. The /apps/tomcat/
folder can then contain many different versions of Tomcat that you need
on the box (e.g /apps/tomcat/5.5.29, /apps/tomcat/6.0.26).

Then under /apps/tomcat/servers/ create your Tomcat homes, each which
represent a runnable server.

The typical structure of a Tomcat home will look like this:

/apps/tomcat/servers/myserver/conf

/webapps

/logs

/work

/temp

Then copy the following files from the installation of tomcat into the
conf directory:

-  

   .. raw:: html

      </p>

   server.xml

   .. raw:: html

      <p>

-  

   .. raw:: html

      </p>

   web.xml

   .. raw:: html

      <p>

.. raw:: html

   </p>

Now you need to create the scripts which will start and stop your Tomcat
home.

Create a startMyServer.sh :

export JAVA\_HOME=/java/jdk1.6

export JAVA\_OPTS="-Dcom.sun.management.jmxremote.port=8086
-Dcom.sun.management.jmxremote.ssl=false
-Dcom.sun.management.jmxremote.authenticate=false -Xmx256m -Xms256m"

export CATALINA\_HOME=/apps/tomcat/5.5.29

export CATALINA\_BASE=/apps/tomcat/myserver

exec "$CATALINA\_HOME"/bin/catalina.sh start

You'll notice that I also enable jmx to allow me to monitor my Tomcat
servers via JMX scripts and tools.

Now Create stopMyServer.sh

export JAVA\_HOME=/java/jdk1.6

export JAVA\_OPTS="-Xmx256m -Xms256m"

export CATALINA\_HOME=/apps/tomcat/5.5.29

export CATALINA\_BASE=/apps/tomcat/myserver

exec "$CATALINA\_HOME"/bin/catalina.sh stop

Once this is done your almost ready to start the server up. However, as
you will have gathered this approach allows you to have multiple servers
running on the same box using a single install. Therefore we need to
ensure that the servers start on different port numbers!

The following should be set unique to the server within the
conf/server.xml file:

Server/Shutdown Port

e.g

HTTP Connector & HTTP Redirect

e.g

AJP Connector & AJP Redirect Connector

e.g.

It is worth deciding on an offset for your ports for each server and
documenting them on your Wiki. I normally use a 1000 offset for each
port of each server.

Now start your server(s) up!

I stress that this solution can also be used instead even when
installing products which utilise Tomcat as their servlet container.
Jira is an example of this, ignore their standalone version (bundled
with tomcat) and go for the WAR download and deploy that into your own
controlled server. :-)

Finally it's worth considering having your Tomcat installs (e.g
/apps/tomcat/6.0.24) on a SAN as this will provide additional value.
Each physical server which is required to host Tomcat servers can then
be provided access to the part of the SAN storing the installations and
the CATALINA\_BASE can be configured to the local storage.


