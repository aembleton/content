WebLogic Cluster commands
#########################
:date: 2009-02-22 04:10
:author: Laurence Noton
:category: Development

Enabling/Disabling cluster debug

 java weblogic.Admin -url t3://localhost:7101 -username weblogic
-password weblogic1 SET -type ServerDebug -property DebugCluster false
