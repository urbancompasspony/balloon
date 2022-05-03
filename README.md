# balloon

This code serve one purpose: Run an auto-balloon on QEMU/KVM virtualized systems!

# How it works?

Analyzing from "virsh dommemstat" parameters "actual" and "usable", the code calculates what % of free memory to be alocated to VM.
If the code detects that VM is running low usable memory, bellow 25%, it will increase it to between 25 and 35%.
If the code detects that VM is running high usable memory, above 35%, it will decrease it to between 25 and 35%.

# Requeriments

Adjust your swappiness inside VM to 10!
Inside /etc/sysctl.conf put this line and reboot your VM:
vm.swappiness=10

# How to run

Put this code where you want, give it permissions to execute and add on Crontab (not sudo crontab!) something like this:

* * * * bash /path/to/automatic
