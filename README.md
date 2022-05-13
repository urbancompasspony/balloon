# auto-ballooning

This code serves one purpose: 
Run an auto-ballooning on QEMU/KVM virtualized systems!

Not works with KSM.

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

1) ANY LINUX VIRTUAL MACHINES.
Unfortunately we can't, for now, use this script on Windows VMs. Even on Windows 7, 8.1, 10 or 11.
The script will just drop and not run over Windows detected VM, passing to another one on host.
The problem is Windows do not alert the KVM the correct amount of buff/cache to inflate or to deflate.
So on my tests, the script will inflate, droping the amount of RAM but can't elevate it if needed. 
As a result, Windows will use lots os page files, being slow until freeze completely.
Still a WIP!

2) Adjust your swappiness inside VM to 10!
Inside /etc/sysctl.conf put this line and reboot your VM:
vm.swappiness=10

With this, the SWAP will be used just when 90% of RAM is being used; this code runs before it, around 80% of RAM being used.

If you would like to use swappiness different from 10, edit the minimum and maximum values inside the code.
Is something like DIY until reach something useful, so use it in test ambience before using it in production.

# How to run

Put this code where you want, give it permissions to execute and add run it 1 time like this:

$ bash /path/to/auto-ballooning &

It will start a Loop and run the code every 0.5 seconds to check if more or less RAM is needed!
Low time to catch any alterations. If you feel that needed, change this to something like 1 or 2 secs.

If you want, you can put it on Crontab (without sudo) with:

@reboot sleep 15; bash /path/to/auto-ballooning

This method it's not guaranteed, the best behavior is running once, manually, at boot of the server!

# Observations

NEVER RENAME THE CODE! Use this name "balloon-2395" when running.
The code will check itself if it is already running on "ps aux" processes, this to NEVER run more than one instance at time!
If the code runs multiple times, on loops, it will reduce VM RAM untill Out Of Memory situations.
