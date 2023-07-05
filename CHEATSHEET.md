# RHCSA cheatsheet

## Users and Groups
1. groupadd newgroup -> create new group
2. useradd newuser -> create new user
3. useradd newuser -G newgroup -> specify secondary group
4. useradd newuser -s /sbin/nologin -> create user without login shell
5. passwd newuser

## chown, chmod, umask
1. chown userxyz:groupxyz file1 - change file ownership
1.5 chown -R -> change recursively
2. chmod 777 file1 - change rwx permissions r=4, w=2, x=1
3. umask -> standard chmod of a user umask 0111 = 0777 - 0111 -> 0666, no execute rights
4. umask 0000 -> -rw-rw-rw-

## ACL File permissions
1. getfacl file -> get ACL permissions
2. setfacl -m u:user1:rw- file1 -> set filepermissions

## setgid
1. chmod g+s /directory/ -> This means that all new files and subdirectories created within the current directory inherit the group ID of the directory, rather than the primary group ID of the user who created the file.

## Change root pw on reboot:
1. Interrupt reboot with e
2. Add rd.break to end of line that starts with linux
3. Ctrl+x to start with new parameters
4. mount –o remount,rw /sysroot
5. chroot /sysroot
6. passwd root
7. touch /.autorelabel

## yum
1. yum config-manager --add-repo http://repo.com/repo -> add repo
2. /etc/yum.repos.d -> repo configuration files
3. /etc/yum.conf -> yum conf file

## Boot into xyz target by default
1. systemctl get-default
2. systemctl set-default xyz

## User Input redirection
1. > redirect into file, overwrite file
2. >> redirect into file, append file
3. | redirect to other processes
4. 2> / 2>> redirect only errors into file

## tar & gzip
1. tar -cf etcb.tar ./etc -> create tar archive from dir /etc (cf = create from file)
2. tar -xf etcb.tar -> extract from file etcb.tar (xf = extract from file)
3. tar -czf etcb.tar.gz etc/ -> make tar and gzip tar
4. gzip archive.tar -> archive.tar.gz

## tuned
1. yum install tuned
2. systemctl enable tuned
3. tuned-adm active -> show active tuned profile
4. tuned-adm list -> list available profiles
5. tuned-adm recommend -> recommend a profile
6. tuned-adm profile <PROFILE> -> set a specific profile
7. tuned-adm off -> turn off tuned-adm

## nice
1. system process priority
2. -20 high -> 19 low
3. nice -n # process -> set process priority
4. renice -n # PID -> adjust process priority

## NTP
configure the NTP server to run at xyz.com
-> NTP will use chrony & chronyd
1. yum install chrony
2. vi /etc/chrony.conf
#pool...
server <GIVEN_IP> iburst
3. systemctl restart chronyd
3.5 systemctl enable chronyd
4. chronyc sources -v
5. timedatectl set-ntp true

## List, create, and delete partitions on GPT disks
1. fdisk -l -> overview over hard drives
2. lsblk -> overview over block storage devices
3. df -h -> overview over mounted partitions
4. fdisb /dev/vdb -> create new partition

## Create, mount, unmount, and use vfat, ext4, and xfs file systems
1. mkfs.xfs /dev/vdb1 -> make partition vdb1 into an xfs file system
2. vi /etc/fstab -> automount partitions on boot
3. /dev/vdb2  /data defaults  0 0
4. volume location    mount folder location   defaults    0   0
## Extend existing logical volumes

pvdisplay
pvcreate
vgdisplay

vgextend
lvextend
