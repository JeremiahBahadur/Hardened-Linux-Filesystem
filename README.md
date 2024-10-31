<h1>Applying Hardened Linux Filesystem Security Controls</h1>


<h2>Description</h2>
Server hardening is the process of securing a system by reducing its surface of vulnerability. It is an important task, especially if you realize that the default configuration of most operating systems is not designed with security as the primary focus. Instead, default setups focus more on usability, communications, and functionality. Protection is provided in various layers and often referred to as defense in depth. The idea behind the defense in depth approach is to defend a system against any particular attack using several independent methods. Defense in depth measures should not only prevent security breaches, but also buy an organization time to detect and respond to an attack, thereby reducing and mitigating the consequences of a breach.

 

This lab incorporates security hardening for file systems and user access to mounted file systems. In this lab, I mounted a file system that does not have execute permissions on the CentOS Linux Server. I also mounted a remote file system and set quotas for users of mounted file systems.


<br />


<h2>Tools and Software</h2>

- Terminal
- vi Editor

<h2>Lab walk-through:</h2>

In the following steps, I hardened security measures on this server by mounting a file system with read-only permissions. I modified the /etc/fstab file and performed several tests to make sure that your changes were effective.
The /etc/fstab file (or file systems table) is a system configuration file commonly found on UNIX systems. This file usually lists all available disks and disk partitions, and indicates how they are to be initialized or otherwise integrated into the overall system's file system.

![image](https://github.com/user-attachments/assets/5a19ed78-da51-40c7-b4f9-6b2b55af872c)
Typical fstab file

In a typical fstab file, the columns are separated by spaces or tabs and defined in the following manner.

 

device name: The device name or other means of locating the partition or data source.

mount point: The mount point, where the data is to be attached to the filesystem. For swap files & devices, this should be set to none.

fs-type: The filesystem type, or the algorithm used to interpret the filesystem.

options: This column indicates whether or not the filesystem should be mounted at boot or whether it can be written to, with rw (read-write) or not, with ro (read-only).

dump-freq: This indicator adjusts the archiving schedule for the partition (used by dump).

pass-num: This indicator controls the order in which fsck checks the device/partition for errors at boot time. The root device should be 1. Other partitions should be either 2 (to check after root) or 0 (to disable checking for that partition altogether).

A value of 0 in either of the last two columns disables the corresponding feature. The /etc/fstab file is most commonly used by the mount command. The mount command instructs the operating system that a file system is ready to use, and it becomes associated with a particular point in the file system’s hierarchy.

Used su -c vi /etc/fstab to launch the vi Editor (-c vi) and open the fstab file. Located the /virtualimages/disk/rimage.img /archives ext3 loop,defaults 0 0 line and applied ro to apply read-only (ro) permissions to the archives directory.

![image](https://github.com/user-attachments/assets/82d442cb-e47b-41f5-834f-2668a4894e9a)

Used the command su -c mount -o remount /archives to remount the archives partition after having made the changes to the fstab file.
Verified that the changes work:

![image](https://github.com/user-attachments/assets/bebbd753-762b-44c1-a979-e8eded82bfa3)
![image](https://github.com/user-attachments/assets/f4bd6e65-e6cc-40ad-a5ca-cf7eda8ce1f2)

Next, I hardened security measures on this server by setting user account quotas on file system partitions. Quotas can be used as a disk management tool to restrict the amount of disk space to which specific users can write.
The following steps required to establish a quota.

A. Add usrquota to the mounting options and mount or remount.
B. Initialize the quota database.
C. Turn quotas on.
D. Edit the user’s quota table to specify a hard limit.

Used the su -c vi /etc/fstab to reopen the fstab file. Located the /virtualimages/disk/qimage.img /common ext3 loop,defaults 0 0 line and typed usrquota to apply user quotas.

![image](https://github.com/user-attachments/assets/2046840a-3536-4e22-bf77-f1a467952b4c)

Remounted the common partition after having made the changes to the fstab file with su -c mount -o remount /common. Initialized the quota database on this folder file system
with su -c /sbin/quotacheck -cug /common. Used su -c /sbin/quotaon /common to turn quotas on.

Opened the file that sets the user’s quota with su -c /usr/sbin/edquota -f /common Student and located the /dev/loop1 line in the Disk quotas for user Student (uid 1000). In the vi editor changed the limits to the following:
First soft column: 30
First hard column: 100

Finally, I tested several scenarios to confirm that my file system changes in the previous lab steps were effective. Created a new directory in the /common folder called Student (mkdir) and (&&) change ownership of that directory (chown) to the Student user account. Implemented command dd if=/dev/urandom of=yourname.txt bs=1M count=100 to use the data duplicator (dd) to create a file with approximately 100MB of random data (bs=1M count=100).

![image](https://github.com/user-attachments/assets/a390d55a-0f91-4a5d-ad6c-6bf7bd2e7419)

Ran a report on the amount of the quota limit already used with su -c /usr/sbin/repquota /common. The repquota report displays a summary of the disk usage and quotas for the specified file systems. The current number of files and amount of space (in kilobytes) is printed for each user, along with any quotas created with edquota. 

![image](https://github.com/user-attachments/assets/4b3a9f1a-47f7-4be8-9ca2-0ec632d052c9)


