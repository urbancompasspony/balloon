# balloon

This code serve one purpose: Run an auto-balloon on QEMU/KVM virtualized systems!
Does not work with KSM.

# How it works?

I was observing how VM's allocate RAM through virt-manager and anothers QEMU/KVM based software.
It let's you put an actual value to RAM and a maximum, but ballooning, the possibility to allocate RAM on live system, does not work automatically.
You need to set this manually as demanded.

This code solves this!
Analyzing from "virsh dommemstat" parameters "actual" and "usable", the code calculates what % of free memory need to be alocated to VM.

I has defined 25 and 35%, a nice value, not so much, but not so low to force VM to use SWAP or going to OOM situations.

If the code detects that VM is running low "usable" memory, bellow 25%, it will increase it to between 25 and 35%.
If the code detects that VM is running high "usable" memory, above 35%, it will decrease it to between 25 and 35%.
If the "usable" memory is between 25 and 35%, then everything is Ok!

# Requeriments

Adjust your swappiness inside VM to 10!
Inside /etc/sysctl.conf put this line and reboot your VM:
vm.swappiness=10

# How to run

Put this code where you want, give it permissions to execute and add on Crontab (not sudo crontab!) something like this:

***** sleep 15; bash /path/to/automatic
***** sleep 30; bash /path/to/automatic
***** sleep 45; bash /path/to/automatic
***** sleep 60; bash /path/to/automatic

This will run the code automatically every 15 seconds to check if more or less RAM is needed!
