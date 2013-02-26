Memory issues on Ubuntu...
##########################
:date: 2011-09-15 04:17
:author: Laurence Noton
:category: Development

Ubuntu 10.04 seems to have an interesting memory issue.  Well I'm not
sure if its an issue per sai or just my lack of knowledge in some area.

 

We have a 4GB VM and with it running only 2 JVMS with a max of
1024+128MB space we're being left with around 60MB of RAM left!!!

 

No idea what is going on, and other not Ubuntu servers seem fine.

Read through
(`http://www.linuxinsight.com/proc\_sys\_vm\_drop\_caches.html`_),

it could be that the OS is keeping inactive memory pages/caches around
just in case it needs to swap them out, and the following command clears
the page caches:

sudo sysctl –w vm.drop\_caches=1

 

I thank a collegue at work for finding this out for me, so all credit
goes to him....although we're not sure we still really understand what
the OS is doing.

 

 

 

 

.. _`http://www.linuxinsight.com/proc\_sys\_vm\_drop\_caches.html`: http://www.linuxinsight.com/proc_sys_vm_drop_caches.html
