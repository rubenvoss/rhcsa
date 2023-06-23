# RHCSA cheatsheet

## Change root pw on reboot:

1. Interrupt reboot with e
2. Add rd.break to end of line that starts with linux
3. Ctrl+x to start with new parameters
4. mount –o remount,rw /sysroot
5. chroot /sysroot
6. passwd root
7. touch /.autorelabel

## Add repos
1. dnf config-manager --add-repo http://repo.com/repo

## Boot into xyz target by default
1. systemctl get-default
2. systemctl set-default xyz

## User Input redirection
1. > redirect into file, overwrite file
2. >> redirect into file, append file
3. | redirect to other processes
4. 2> / 2>> redirect only errors into file

## chown, chmod, umask
1. chown userxyz:groupxyz file1 - change file ownership
2. chmod 777 file1 - change rwx permissions r=4, w=2, x=1
3. umask -> standard chmod of a user umask 0111 = 0777 - 0111 -> 0666, no execute rights
4. umask 0000 -> -rw-rw-rw-

## tar & gzip
1. tar -cf etcb.tar ./etc -> create tar archive from dir /etc (cf = create from file)
2. tar -xf etcb.tar -> extract from file etcb.tar (xf = extract from file)
3. tar -czf etcb.tar.gz etc/ -> make tar and gzip tar
4. gzip archive.tar -> archive.tar.gz
