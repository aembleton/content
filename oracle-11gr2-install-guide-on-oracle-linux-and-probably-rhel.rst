Oracle 11gR2 install guide on Oracle Linux (and probably RHEL)
##############################################################
:date: 2012-03-24 14:01
:author: Laurence Noton
:category: Development

**This guide focuses more on perparing the environment for
installation.**

**Install/Update OS**

Configure YUM repo (http://public-yum.oracle.com/)

yum update

**Create groups and Users**

groupadd  oinstall

groupadd  dba

useradd -m -g oinstall -G dba -d /home/oracle -s /bin/bash -c "Oracle
Software Owner" oracle

passwd oracle

 

**Kernel Parameters**

 

Change the /etc/sysctl.conf file to set kernel parameters that are
required for Oracle installation

The file should be as follows:

net.ipv4.ip\_forward = 0

net.ipv4.conf.default.rp\_filter = 1

net.ipv4.conf.default.accept\_source\_route = 0

kernel.sysrq = 0

kernel.core\_uses\_pid = 1

net.ipv4.tcp\_syncookies = 1

kernel.msgmnb = 65536

kernel.msgmax = 65536

kernel.shmmax = 2147483648

kernel.shmall = 2097152

kernel.shmmni = 4096

kernel.sem = 250 32000 100 128

fs.file-max = 6815744

fs.aio-max-nr = 1048576

net.ipv4.ip\_local\_port\_range = 9000 65500

net.core.rmem\_default = 1048576

net.core.rmem\_max = 4194304

net.core.wmem\_default = 262144

net.core.wmem\_max = 1048576

run /sbin/sysctl -p command

Change /etc/pam.d/login file and add the following line:

session required pam\_limits.so

Change /etc/security/limits.conf and add the following lines:

oracle              soft    nproc   2047

oracle              hard    nproc   16384

oracle              soft    nofile  1024

oracle              hard    nofile  65536

 

**Perpare install location**

Create necessary directories for Oracle 11g installation:

mkdir -p /u01/app/oracle/product/11.2.0/db\_1

chown -R oracle:oinstall /u01

vi /home/oracle/.bash\_profile

export ORACLE\_SID=orcl

export ORACLE\_HOME=/u01/home/oracle/product/11.2.0/db\_1

export LD\_LIBRARY\_PATH=$LD\_LIBRARY\_PATH:$ORACLE\_HOME/lib

export PATH=$ORACLE\_HOME/bin:$PATH

Download Oracle 11.2 g zip files and unzip into /raw\_install

chown -R oracle:oinstall /raw\_install/database

Log out of 'root', and log back in as 'oracle'

cd /raw\_install/database

./runInstaller

Follow the wizard through for Desktop class and everything should be
default accepts.

Note the globle database name 'orcl'
