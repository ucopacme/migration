Preparing Opsmon DEV environment for migration
==============================================

The process of migrating the VMware virtual machines from SDSC to LDC@AWS will use the service called SMS (Server Migration Service)

This will essentially clone the VMware VM and send it over the wire to the LDC account and create an AMI template that can then be used to create ec2 instances from that AMI template. A new AMI of the VMware vm is created every 12 hours based off the configuration given upon setting up the replication of the VM. 

Example code used to create AMI
::

  aws ec2 run-instances --image-id ami-0a02244c  --count 1 --instance-type t2.micro --security-group-ids sg-xxxxxxx --associate-public-ip-address --private-ip-address 10.48.76.165 --subnet-id subnet-xxxxx --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=myec2name}]'




Tasks Completed:
---------------

- Import updated catalog of VMware virtual machines that are located on p-sdsc-vcenter to the LDC

- Create AMI replication schedule of opsmond01/2/3 and d-its-ca07 
	- replication runs every 12 hours with a new AMI being built

-
