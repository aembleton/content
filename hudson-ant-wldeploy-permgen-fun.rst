Hudson Ant & WLDeploy (Permgen fun)
###################################
:date: 2010-02-02 05:24
:author: Laurence Noton
:category: Development

A recent problem I've experienced is *PermGen OutOfMemoryError* when
having our continous integration ANT script deploy built artifacts to
WebLogic 10.3.2 CI server after extensive build and unit tests.

Stack traces seemed to indicate a potential issue with WebLogic (we
suffer perm gen issues with the constant redeploy on system and
integration testing) however further analysis showed that it was
actually the permgen of the forked JVM which is kicked off by Hudson to
run Ant.

We monitored this forked JVM by setting the java options:

-Dcom.sun.management.jmxremote.port=8087
-Dcom.sun.management.jmxremote.ssl=false
-Dcom.sun.management.jmxremote.authenticate=false

And connecting by JConsole
(service:jmx:rmi:///jndi/rmi://”+hostname+”:”+port+”/jmxrmi) we could
see the PermGen maxed out.

This was easily fixed by -XX:MaxPermSize=128m
