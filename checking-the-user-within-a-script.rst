Checking the user within a script.
##################################
:date: 2012-02-14 03:56
:author: Laurence Noton
:category: Script

Very useful to prevent scripts being run as the wrong user (i.e root),
and messing up subsequent permission changes from log files etc.

 

actuid=\`id tomcat \| sed 's/uid=\\([0-9]\*\\).\*/\\1/g'\`

NOTAU=64

[ $UID -ne $actuid ] && echo "Must be run as tomcat" && exit $NOTAU \|\|
echo "Running as correct user"
