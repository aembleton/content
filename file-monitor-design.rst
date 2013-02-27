Endpoint Monitor Design
#######################
:date: 2013-02-26 11:36
:author: Laurence Noton
:category: Design

Problem:  Monitor an endpoint (sftp, directory or any other resource) with a configurable schedule (poll, cron etc).

Solution:

.. image:: static/images/filemonitor.png


Above is a simple extensible object model to create message receivers and associate them with a particular connector.  The illustration shows two different message receivers (poller and cron).  They are associated with a connector (in this case SFTPConnector) by calling a register() method passing itself as a parameter.  

The connector can then be started by calling start(), which intern would start all registered receivers.  During start-up of the receiver, the receiver will calculate its next run time and then request the connector to execute its run() method at that time.  On execution of the run, the receiver would then request the next run time to the connector.  The connector makes use of the ExecutorService so everything is based on fixed delays (polling) or milliseconds to run (cron).

Being framework independent means that the whole configuration needs to be bootstrapped by code, however, this is a minor issue and opens up a few important extensions:  Each message receiver can receive its configuration from a central place (ZooKeeper anyone?).   As a configuration change is made (change of cron, file location for example) the receiver would be automatically notified, could stop, set the new configuration and then start up again.

Alternatives:

Spring integration - the sftp adapter doesn't let you check to ensure the file isn't still being written to (i.e. stable) - we deal with very large files!  Of course you could do this yourself, but your design becomes messy.  The integration layer should just do this in a self contained cohensive unit.

<insert name> Service Bus - Most don't check file size so we have the same problem as above, and most are just too heavy weight for what we want.

