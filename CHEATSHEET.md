# RHCSA cheatsheet
## Understand and use essential tools
### Use input-output redirection (>, >>, |, 2>, etc.)
1. '>' redirect into file, overwrite file
2. '>>' redirect into file, append file
3. | redirect to other processes
4. 2> / 2>> redirects only errors into file

### Log in and switch users in multi-user targets
1. runlevel
2. systemctl get-default -> get runlevel
3. systemctl set-default multi-user.target -> set multiuser runlevel
4. reboot

### tar & gzip
1. tar -cf etcb.tar ./etc -> create tar archive from dir /etc (cf = create from file)
2. tar -xf etcb.tar -> extract from file etcb.tar (xf = extract from file)
3. tar -czf etcb.tar.gz etc/ -> make tar and gzip tar
4. gzip archive.tar -> archive.tar.gz

## Create simple shell scripts
### Conditionally execute code (use of: if, test, [], etc.)
if [ $1 = 100 ]
then
  echo "100"
else
  echo "not 100"
fi

while [ $1 -lt 100 ]
do
  echo $1
  $1 = $1 + 1
done
### Use Looping constructs (for, etc.) to process file, command line input
for file in ./*.log
do
echo "log file: $file"
done

### Processing output of shell commands within a script
for file in $(ls)
for file in `ls`

## Operate running systems
### Boot, reboot, and shut down a system normally
1. shutdown -h now
2. reboot
3. systemctl reboot

### Boot systems into different targets manually
1. systemctl get-default
2. systemctl list-units --type target
3. systemctl set-default multi-user.target
4. systemctl isolate multi-user.target

### Interrupt the boot process in order to gain access to a system:
1. Interrupt reboot with e
2. change ro -> rw
3. Add rd.break to end of line that starts with linux
4. Ctrl+x to start with new parameters
5. mount –o remount,rw /sysroot
6. chroot /sysroot
7. passwd root
8. touch /.autorelabel

### Identify CPU/memory intensive processes and kill processes
1. top
2. shift + m -> sort by memory
3. shift + p -> sort by cpu
4. kill -9 PID
5. killall -9 processname

### Adjust process scheduling
1. nice -n 19 command -> set process priority
2. renice -n 19 -p PID -> adjust process priority

### Manage tuning profiles
1. tuned-adm list -> list available profiles
2. tuned-adm active -> show active profile
3. tuned-adm recommend -> recommend a profile
4. tuned-adm profile <PROFILE> -> set a specific profile

### Locate and interpret system log files and journals
1. cat /var/log/messages
2. cat /var/log/secure
3. journalctl -xe -> show all logs
4. journalctl
5. journalctl -u httpd -> show logs for httpd
6. journalctl -p err -> show logs with priority err
7. journalctl -k -> show kernel logs
8. journalctl -n 10 -> show last 10 lines
9. journalctl -f -> follow log
10. journalctl --since "today" -> show logs since today

### Preserve system journals
1. mkdir /var/log/journal -> create journal directory
2. vi /etc/systemd/journald.conf
3. Storage=persistent
4. SystemMaxUse=100M
5. systemctl restart systemd-journald
### Start, stop, and check the status of network services
1. systemctl status httpd
2. systemctl start httpd
3. systemctl enable httpd
4. systemctl mask httpd -> disable httpd
5. systemctl unmask httpd -> enable httpd

## Configure local storage
### List, create, and delete partitions on GPT disks
1. lsblk -> overview over block storage devices
2. fdisk -l -> overview over hard drives
3. fdisk /dev/vdb -> create new partition
4. df -h -> overview over mounted partitions
5. fdisk /dev/vdb -> create new partition

### Create and remove physical volumes
1. pvdisplay -> display physical volumes
2. pvcreate /dev/vdb1 -> create physical volume
3. pvremove /dev/vdb1 -> remove physical volume

### Assign physical volumes to volume groups
1. vgdisplay / vgs -> display volume groups
2. vgcreate vg1 /dev/vdb1 -> create volume group vg1
3. vgextend vg1 /dev/vdb2 -> add physical volume to volume group
4. vgreduce vg1 /dev/vdb2 -> remove physical volume from volume group
5. vgremove vg1 -> remove volume group

### Create and delete logical volumes
1. lvdisplay / lvs -> display logical volumes
2. lvcreate -L 1G -n lv1 vg1 -> create logical volume lv1 with 1GB from volume group vg1
3. lvextend -L +1G /dev/vg1/lv1 -> extend logical volume lv1 by 1GB
4. lvresize -L -1G /dev/vg1/lv1 -> shrink logical volume lv1 by 1GB
5. lvremove /dev/vg1/lv1 -> remove logical volume lv1

### Configure systems to mount file systems at boot by universally unique ID (UUID) or label
1. blkid -> show UUIDs
2. blkid | grep '/dev/lv1' >> /etc/fstab -> add UUID to fstab
3. vi /etc/fstab
4. UUID=xyz /mnt ext4 defaults 0 2
5. mount -a -> mount all partitions in fstab & test /etc/fstab before reboot

## Add new partitions and logical volumes, and swap to a system non-destructively
1. lvcreate
2. swapon

## Create, mount, unmount, and use vfat, ext4, and xfs file systems
1. mkfs.xfs /dev/vg1/lv1
2. mount /dev/vg1/lv1 /mnt
3. vi /etc/fstab
4. mount -a -> test /etc/fstab
### Use grep and regular expressions to analyze text
1. grep 'xyz' file1 -> search for xyz in file1
2. grep '[AaBb]nanas' file1 -> search for Ananas and Bananas in file1
3. grep '^Jul 15' Linux_2k.log -> search for all lines starting with Jul 15
4. grep '[^d]og' file1 -> search for all lines containing og but not dog
5. grep 'st..id' -> search for all lines containing st followed by any two characters followed by id (e.g. stupid, sts3id, st!did)
6. grep 'r*' -> search for all lines containing r followed by any number of characters
7. grep 'r?uben' -> search for all lines containing r followed by any one character followed by uben (e.g. ruben, r3uben, r!uben)
8. grep -v -> return inverse results
9. ls -l | grep '.md$' -> return all files ending with .md

### Users and Groups
1. groupadd newgroup -> create new group
2. useradd newuser -> create new user
3. useradd newuser -G newgroup -> specify secondary group
4. useradd newuser -s /sbin/nologin -> create user without login shell
5. passwd newuser

### chown, chmod, umask
1. chown userxyz:groupxyz file1 - change file ownership
1.5 chown -R -> change recursively
2. chmod 777 file1 - change rwx permissions r=4, w=2, x=1
3. umask -> standard chmod of a user umask 0111 = 0777 - 0111 -> 0666, no execute rights
4. umask 0000 -> -rw-rw-rw-

### ACL File permissions
1. getfacl file -> get ACL permissions
2. setfacl -m u:user1:rw- file1 -> set filepermissions

## SELinux
1. yum install policycoreutils-python-utils -> install semanage
### Set enforcing and permissive modes for SELinux
1. vi /etc/selinux/config
2. SELINUX=disabled / permissive / enforcing
3. reboot
4. sestatus

### List and identify SELinux file and process context
1. ls -lZ -> file context
2. ls -dZ -> directory context
3. ps -auxZ -> process context

### Restore default file contexts
1. restorecon file1
2. restorecon -R /directory/

### Manage SELinux port labels
1. man semanage-port
2. semanage port -l -> list all ports
3. semanage port -a -t http_port_t -p tcp 82 -> system can connect to httpd on port 82
4. semanage port -d -t http_port_t -p tcp 82 -> system can no longer connect to httpd on port 82

### Use boolean settings to modify system SELinux settings
1. semanage boolean -l -> list all booleans
2. getsebool -a -> list all booleans
3. setsebool -P <BOOLEAN> on -> set boolean permanently

### Diagnose and address routine SELinux policy violations
1. ausearch -c 'httpd'
2. sealert -a /var/log/audit/audit.log

### setgid
1. chmod g+s /directory/ -> This means that all new files and subdirectories created within the current directory inherit the group ID of the directory, rather than the primary group ID of the user who created the file.

### yum
1. yum config-manager --add-repo http://repo.com/repo -> add repo
2. /etc/yum.repos.d -> repo configuration files
3. /etc/yum.conf -> yum conf file

### tuned
1. yum install tuned
2. systemctl enable tuned
3. tuned-adm active -> show active tuned profile
4. tuned-adm list -> list available profiles
5. tuned-adm recommend -> recommend a profile
6. tuned-adm profile <PROFILE> -> set a specific profile
7. tuned-adm off -> turn off tuned-adm

### nice
1. system process priority
2. -20 high -> 19 low
3. nice -n # process -> set process priority
4. renice -n # PID -> adjust process priority

### NTP
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



### Create, mount, unmount, and use vfat, ext4, and xfs file systems
1. mkfs.xfs /dev/vdb1 -> make partition vdb1 into an xfs file system
2. vi /etc/fstab -> automount partitions on boot
3. mount -a -> test /etc/fstab
4. /dev/vdb2  /data defaults  0 0
5. volume location    mount folder location   defaults    0   0
### Extend existing logical volumes

pvdisplay
pvcreate
vgdisplay

vgextend
lvextend
