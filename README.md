# auto-ballooning

This code serves one simple purpose: 
Run an auto-ballooning on QEMU/KVM virtualized systems!
Tested on Ubuntu Server 22.04 LTS VM's but can be used with other distros.

Not works with KSM.
Not works with Windows; nothing that has Microsoft on its name! Please, do not insist.

# How it works?

I was observing how VM's allocate RAM through virt-manager and anothers QEMU/KVM based software.
It let's you put an actual value to RAM and a maximum, but ballooning, the possibility to allocate RAM on live system, does not work automatically.
You need to set this manually as demanded.

This code solves this! (some)
Analyzing from "virsh dommemstat" parameters "actual" and "usable", the code calculates what % of free memory need to be alocated to VM.

I has defined 28 and 38%, not so much but not so low, to force VM to use SWAP or going to OOM situations.

If the code detects that VM is running low "usable" memory, bellow 28%, it will increase it to between 28 and 38%.
If the code detects that VM is running high "usable" memory, above 38%, it will decrease it to between 28 and 38%.
If the "usable" memory is between 28 and 38%, then everything is Ok!

# Emergency Stop

I put a dead volume value, that is 300 Mb, safe value for generic purposes. You can change that inside the script.
Linux generally OOM and freezes with around 230 Mb reported by virt-manager (that is 159 Mb real used inside VM).

All depends of pressure under SWAP the kernel of the VM will handle (not swappiness, this is another thing!)
For example, under some circunstances, Virt-Manager can say 340 Mb are set to VM, where 180 Mb is in use (52% os total).
So theorically you can grab 12% of this; but if you force to 20% this will turn on OOM! Sometimes virt-manager maths are a mistery.

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

Also you can put it on Crontab (WITH SUDO) with:

@reboot /path/to/auto-ballooning &

# Observations

If the code runs multiple times, on loops, it will reduce VM RAM untill Out Of Memory situations.
So NEVER run this twice at a time! If it is already running, close it first.
WIP to auto-check if it is already running!
