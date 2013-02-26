Connecting to WebLogic JMX via JConsole
#######################################
:date: 2010-05-04 09:22
:author: Laurence Noton
:category: Development

Start JConsole via:

C:\\Oracle\\Middleware\\jdk160\_14\_R27.6.5-32\\bin>

jconsole

-J-Djava.class.path=%JAVA\_HOME%\\lib\\jconsole.jar;c:\\Oracle\\Middleware\\wlserver\_10.3\\server\\lib\\wljmxclient.jar

-J-Djmx.remote.protocol.provider.pkgs=weblogic.management.remote

Ensure that IIOP has been enable under Environments -> Servers -> ->
Protocols -> IIOP.

Then enter the follow for the runtime mbean:

service:jmx:rmi:///jndi/iiop://127.0.0.1:7001/weblogic.management.mbeanservers.runtime

This is where your custom MBeans will appear (see previous article).
