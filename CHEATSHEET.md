# RHCSA cheatsheet
## Understand and use essential tools
### Use input-output redirection (>, >>, |, 2>, etc.)
1. '>' redirect into file, overwrite file
2. '>>' redirect into file, append file
3. | redirect to other processes
4. 2> / 2>> redirects only errors into file

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

### Log in and switch users in multi-user targets
1. runlevel
2. systemctl get-default -> get runlevel
3. systemctl set-default multi-user.target -> set multiuser runlevel
4. reboot
5. su - user1 -> switch user

### tar & gzip
1. tar -cf etcb.tar ./etc -> create tar archive from dir /etc (cf = create from file)
2. tar -xf etcb.tar -> extract from file etcb.tar (xf = extract from file)
3. tar -czf etcb.tar.gz etc/ -> make tar and gzip tar
4. gzip archive.tar -> archive.tar.gz

### List, set, and change standard ugo/rwx permissions
1. chown userxyz:groupxyz file1 - change file ownership
1.5 chown -R -> change recursively
2. chmod 777 file1 - change rwx permissions r=4, w=2, x=1
3. umask -> standard chmod of a user umask 0111 = 0777 - 0111 -> 0666, no execute rights
4. umask 0000 -> -rw-rw-rw-


## Create simple shell scripts
### Conditionally execute code (use of: if, test, [], etc.)
man test
if [ $1 -eq 100 ]
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

for file in $(ls /var/log)
do
  echo "log file: $file"
done

### Processing shell command exit codes
1. echo $? -> show exit code of last command
if [ $? -eq 0 ]
then
  echo "success"
else
  echo "failure"
fi

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
1. nice -n 19 command -> start process with low priority
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
2. lvcreate vg1 -L 1G -n lv1  -> create logical volume lv1 with 1GB from volume group vg1
3. lvextend -L +1G /dev/vg1/lv1 -> extend logical volume lv1 by 1GB
4. lvresize -L -1G /dev/vg1/lv1 -> shrink logical volume lv1 by 1GB
5. lvremove /dev/vg1/lv1 -> remove logical volume lv1

### Configure systems to mount file systems at boot by universally unique ID (UUID) or label
1. blkid -> show UUIDs
2. blkid | grep '/dev/lv1' >> /etc/fstab -> add UUID to fstab
3. vi /etc/fstab
4. UUID=xyz /mnt ext4 defaults 0 2
5. mount -a -> mount all partitions in fstab
6. findmnt --verify -> test /etc/fstab before reboot

## Add new partitions and logical volumes, and swap to a system non-destructively
1. lvcreate
2. swapon

## Create, mount, unmount, and use vfat, ext4, and xfs file systems
1. mkfs.xfs /dev/vg1/lv1
2. mount /dev/vg1/lv1 /mnt
3. vi /etc/fstab
4. mount -a -> mount /etc/fstab
5. findmnt --verify -> verify /etc/fstab

## Mount and unmount network file systems using NFS

## Configure autofs

## Extend existing logical volumes

### setgid
1. chmod g+s /directory/ -> This means that all new files and subdirectories created within the current directory inherit the group ID of the directory, rather than the primary group ID of the user who created the file.
### Create and configure set-GID directories for collaboration
1. groupadd <GROUP>
2. cat /etc/group
3. mkdir -p /directory/
4. chown nobody:GROUP /directory/
5. chmod g+s /directory/
6. chmod 770 /directory/

### Configure disk compression
1. yum install vdo
2. empty disk with no partitions
3. vdo create --name=vdo1 --device=/dev/sdc --vdoLogicalSize=10G --writePolicy=auto 
4. mkfs.xfs /dev/mapper/vdo1
5. mount /dev/mapper/vdo1 /mnt

### Manage layered storage
1. yum install stratis-cli
2. systemctl start stratisd
3. systemctl enable stratisd
4. stratis pool create pool1 /dev/sdb /dev/sdc
5. stratis fs create pool1 fs1
6. stratis fs list
7. mount /dev/stratis/pool1/fs1 /mnt
8. stratis pool add-data pool1 /dev/sdd
9. stratis fs snapshot pool1 fs1 fs1-snap1
10. stratis fs list
11. stratis fs destroy pool1 snapshot1
12. stratis pool destroy pool1
13. stratis pool list

### Diagnose and correct file permission problems
1. getfacl file1 -> get ACL permissions

## Deploy, configure, and maintain systems

### Schedule tasks using at and cron
1.timedatectl && timedatectl set-timezone
2. yum install at
3. systemctl start atd && systemctl enable atd
4. at now + 1min
5. at 01:00 november 1
6. atrm <JOBID>
7. crontab -l -> list
6. crontab -e -> edit
7. minute hour day(month) month day(week)
8. ls -l /etc/cron.daily/

### Start and stop services and configure services to start automatically at boot
1. systemctl start httpd
2. systemctl enable httpd -> enable service on boot
3. systemctl reload httpd -> reload config
4. systemctl restart httpd -> restart service
5. systemctl mask httpd -> disable service

### Configure systems to boot into a specific target automatically
1. systemctl list-units --type target
2. systemctl get-default
3. systemctl set-default multi-user.target -> set runlevel on boot
4. systemctl isolate multi-user.target -> change runlevel on the fly
5. systemctl reboot


### Configure time service clients
1. timedatectl set-timezone Europe/Berlin
2. timedatectl set-ntp true
3. chronyc sources -v
4. vi /etc/chrony.conf
5. systemctl restart chronyd

### Install and update software packages from Red Hat Network, a remote repository, or from the local file system
1. yum search <PACKAGE>
2. yum list <PACKAGE>
3. yum install <PACKAGE>
4. yum update <PACKAGE>
5. yum localinstall <PACKAGE>
6. yum remove <PACKAGE>
7. yum provides <PACKAGE>
8. yum repolist
9. yum history
10. yum history rollback <ID>
11. yum history undo <ID>
12. yum history redo <ID>


13. yum install dnf-utils
14. yum config-manager --add-repo http://repo.com/repo
15. /etc/yum.repos.d -> repo configuration files
16. /etc/yum.conf -> yum conf file
17. yum install createrepo
18. createrepo /path/to/repo
19. yum config-manager --add-repo file:///path/to/repo
20. yum repolist
21. yum disable <REPO>


### Work with package module streams
1. dnf module list
2. dnf module info --profile <MODULE>
3. dnf module install <MODULE>
4. dnf module install <MODULE>:8.0/minimal
5. dnf module remove <MODULE>
6. dnf module reset <MODULE>

### Modify the system bootloader
1. vi /etc/default/grub
2. grub2-mkconfig -o /boot/grub2/grub.cfg

## Manage basic networking

### Configure IPv4 and IPv6 addresses
1. nmcli connection show
2. ip addr
3. nmcli device status
4. nmcli device show enp0s8
5. nmcli connection show
6. nmcli connection add con-name eth0 ifname enp0s8 type ethernet ip4 10.0.2.16/24
7. nmcli connection up eth0
8. nmcli connection down eth0
9. nmcli connection modify eth0 ipv4.addresses 10.0.2.15/24
10. nmcli connection modify eth0 ipv4.gateway 10.0.2.1
11. nmcli connection modify eth0 ipv4.method manual
12. nmcli connection down eth0
13. nmcli connection up eth0

14. nmcli connection add con-name eth0 ifname enp0s8 type ethernet ip6 2006:ac81::1105/64 gw6 2006:ac81:: 1101
15. nmcli connection modify eth0 ipv6.addresses 2006:ac81::1105/64
16. nmcli connection modify eth0 ipv6.gateway 2006:ac81::1105
17. nmcli connection modify eth0 ipv6.method manual
18. nmcli connection down eth1
19. nmcli connection up eth1

20. nmtui

### Configure hostname resolution
1. hostnamectl set-hostname xyz.com
2. hostname
3. cat /etc/resolv.conf
4. nmcli connection modify enp0s3 ipv4.dns "8.8.8.8"
5. nmcli connection reload
6. systemctl restart NetworkManager
7. cat /etc/relov.conf
8. vi /etc/hosts
9. 10.0.2.15 testserver.local testserver
10. ping testserver

### Configure network services to start automatically at boot
1. systemctl...
2. nmcli con mod eth0 connection.autoconnect yes -> enable connection on boot

### Restrict network access using firewall-cmd/firewalld
1. firewall-cmd --get-zones
2. firewall-cmd --new-zone xyz --permanent
3. firewall-cmd --reload
4. firewall-cmd --zone xyz --add-service http --permanent
5. firewall-cmd --reload
6. firewall-cmd --zone xyz --list-all
7. firewall-cmd --change-interface enp0s8 --zone xyz --permanent
8. firewall-cmd --get-active-zones
9. firewall-cmd --set-default-zone xyz
10. firewall-cmd --get-default-zone
11. firewall-cmd --reload
12. firewall-cmd --get-services
13. firewall-cmd --add-port 8080/tcp --permanent
14. firewall-cmd --reload
15. firewall-cmd --zone xyz --remove-service http --permanent
16. firewall-cmd --reload
17. firewall-cmd --zone xyz --remove-port 8080/tcp --permanent
18. firewall-cmd --reload

## Manage users and groups

### Create, delete, and modify local user accounts
1. useradd user1
2. passwd user1
3. groupadd group1
4. usermod -aG group1 user1
5. userdel user1
6. userdel -r user1 -> delete user and home directory

### Change passwords and adjust password aging for local user accounts
1. chage -l user1 -> show password aging
2. chage -M 90 user1 -> set max password age to 90 days
3. chage ruben -> change password aging for ruben

### Create, delete, and modify local groups and group memberships
1. groupadd group1
2. groupdel group1
3. groupmod -n group2 group1 -> rename group1 to group2
4. groupmems -g group1 -l -> list group members
5. groupmems -g group1 -d user1 -> delete user1 from group1
6. groupmems -g group1 -a user1 -> add user1 to group1

### Configure superuser access
1. visudo
2. ruben ALL=(root) /bin/ls, bin/df -h -> allow user ruben to run ls and df -h as root
3. USERNAME HOSTNAME=(USER) COMMAND -> allow user USERNAME to run COMMAND as USER on HOSTNAME

## Manage security

### Configure firewall settings using firewall-cmd/firewalld
1. vi /etc/sysctl.conf
2. net.ipv4.ip_forward=1
3. sysctl -p -> reload sysctl.conf
4. sysctl -a | grep ip_forward -> check if ip_forward is enabled
5. dnf install firewall-config (GUI)

### Create and use file access control lists
1. getfacl file1 -> get ACL permissions
2. setfacl -m u:user1:rw- file1 -> set filepermissions for specific user
3. setfacl -m g:group1:rw- file1 -> set filepermissions for specific group
4. setfacl -x u:user1 file1 -> remove filepermissions for specific user
5. setfacl -R -m u:user1:rw- dir1/ -> set filepermissions for specific user recursively

### Set enforcing and permissive modes for SELinux
1. sestatus -> show SELinux status
2. vi /etc/selinux/config
3. SELINUX=disabled / permissive / enforcing
4. reboot
5. sestatus

### List and identify SELinux file and process context
1. ls -lZ -> file context
2. ls -dZ -> directory context
3. ps -auxZ -> process context


### Restore default file contexts
1. chcon unconfined_u:object_r:tmp_t:s0 file1
2. restorecon file1
3. restorecon -R /directory/

### Manage SELinux port labels
1. man semanage-port
2. semanage port -l -> list all ports
3. semanage port -a -t http_port_t -p tcp 82 -> system can connect to httpd on port 82
4. semanage port -d -t http_port_t -p tcp 82 -> system can no longer connect to httpd on port 82


### Use boolean settings to modify system SELinux settings
1. semanage boolean -l -> list all booleans
2. getsebool -a -> list all booleans
3. setsebool -P <BOOLEAN> on -> set boolean permanently (P)

### Diagnose and address routine SELinux policy violations
1. yum install setroubleshoot-server
2. sealert -a /var/log/audit/audit.log
3. ausearch -c 'httpd'
4. grep 1415714880.156:29 /var/log/audit/audit.log | audit2why


### NTP
configure the NTP server to run at xyz.com
-> NTP will use chrony & chronyd
1. yum install chrony
2. vi /etc/chrony.conf
#pool...
server <GIVEN_IP> iburst
1. systemctl restart chronyd
3.5 systemctl enable chronyd
1. chronyc sources -v
2. timedatectl set-ntp true


### Users and Groups
1. groupadd newgroup -> create new group
2. useradd newuser -> create new user
3. useradd newuser -G newgroup -> specify secondary group
4. useradd newuser -s /sbin/nologin -> create user without login shell
5. passwd newuser
6. usermod user -aG group -> add user to group

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





### Create, mount, unmount, and use vfat, ext4, and xfs file systems
1. mkfs.xfs /dev/vdb1 -> make partition vdb1 into an xfs file system
2. vi /etc/fstab -> automount partitions on boot
3. mount -a -> mount /etc/fstab
4. findmnt --verify -> test /etc/fstab
5. /dev/vdb2  /data defaults  0 0
6. volume location    mount folder location   defaults    0   0
### Extend existing logical volumes

pvdisplay
pvcreate
vgdisplay

vgextend
lvextend
