edit /etc/resolve.conf
----------------------

ec2 uses DHCP to generate /etc/resolve.conf poining to the VPC default 
name service.  this brakes puppet, because it can not resolve the puppet
servers' hostnames.

I think DHCP rewrites it every reboot


create a hiera node file
------------------------

delete lvm stuff from hiera node files
ec2 instances do not use the same disk device names as SDCS VMs


reset puppet cert
-----------------

this is normal for a new puppet node
