#测试连续两次复制同一文件至同一目的路径，文件属性（时间/文件名称）是否受影响
[kingarthur@localhost ~]$ pwd
/home/kingarthur
[kingarthur@localhost ~]$ su root
Password: 
#第一次复制文件并查看文件属性
[root@localhost kingarthur]# cp -i ~/.bashrc /tmp/bashrc
cp: overwrite ‘/tmp/bashrc’? n
[root@localhost kingarthur]# ls -l --full-time /tmp/bashrc
-rw-r--r--. 1 root root 176 2020-03-30 01:12:20.698920937 +0800 /tmp/bashrc
#第二次复制文件并查看文件属性，交互“是否覆盖”？
[root@localhost kingarthur]# cp -i ~/.bashrc /tmp/bashrc
cp: overwrite ‘/tmp/bashrc’? Y
[root@localhost kingarthur]# ls -l --full-time /tmp/bashrc
-rw-r--r--. 1 root root 176 2020-03-30 01:17:23.255914128 +0800 /tmp/bashrc
##文件权限、链接数、所有者、群组、文件大小均为发生改变；时间发生改变，发生文件覆盖


      
#测试文件复制（源文件、链接文件复制差异）
#查看目录结构
[root@localhost tmp]# ls -l --full-time ./bashrc /home/kingarthur/.bashrc
-rw-r--r--. 1 root       root       176 2020-03-30 01:17:23.255914128 +0800 ./bashrc
-rw-r--r--. 1 kingarthur kingarthur 231 2018-10-31 01:07:12.000000000 +0800 /home/kingarthur/.bashrc
#复制文件到当前路径
[root@localhost tmp]# cp /var/log/wtmp .
#查看文件属性
[root@localhost tmp]# ll --full-time  /var/log/wtmp ./wtmp
-rw-rw-r--. 1 root utmp 42240 2020-03-30 00:15:15.053998029 +0800 /var/log/wtmp
-rw-r--r--. 1 root root 42240 2020-03-30 01:28:49.632898682 +0800 ./wtmp
##文件群组权限、群组、时刻发生改变
#复制文件至当前路径并更改文件名称
[root@localhost tmp]# cp /var/log/wtmp ./wtmp2
#查看文件属性
[root@localhost tmp]# ll -a --full-time  /var/log/wtmp ./wtmp2
-rw-rw-r--. 1 root utmp 42240 2020-03-30 00:15:15.053998029 +0800 /var/log/wtmp
-rw-r--r--. 1 root root 42240 2020-03-30 01:33:05.401892926 +0800 ./wtmp2
##群组权限、群组时间发生改变
#指定参数 -a 保持文件全部原有属性，不更改
[root@localhost tmp]# cp -a /var/log/wtmp ./wtmp3
[root@localhost tmp]# ll -a --full-time  /var/log/wtmp ./wtmp3
-rw-rw-r--. 1 root utmp 42240 2020-03-30 00:15:15.053998029 +0800 /var/log/wtmp
-rw-rw-r--. 1 root utmp 42240 2020-03-30 00:15:15.053998029 +0800 ./wtmp3
[root@localhost tmp]# 





[root@localhost kingarthur]# cd /tmp/
[root@localhost tmp]# pwd
/tmp
[root@localhost tmp]# cp /etc/ /tmp/
cp: omitting directory ‘/etc/’
#参数 -r 递归持续复制
[root@localhost tmp]# cp -r /etc/ /tmp/
[root@localhost tmp]# ls ./etc
abrt                       dhcp                        hosts                     man_db.conf          popt.d            slp.spi
adjtime                    DIR_COLORS                  hosts.allow               maven                portreserve       smartmontools
#参数 -d 仅显示目录本身
[root@localhost tmp]# ls -d ./etc
./etc
[root@localhost tmp]# ls ./bashrc 
./bashrc
#参数 -s 复制成为符号链接文件（symbolic link）,亦即快捷方式文件
[root@localhost tmp]# cp -s bashrc bashrc_slink
#参数 -l 进行硬式链接（hard link）的链接挡建立，而非复制文件本身
[root@localhost tmp]# cp -l bashrc bashrc_hlink
#查看复制结果，符号链接类似“快捷方式”
[root@localhost tmp]# ls -l bashrc*
-rw-r--r--. 2 root root 176 Mar 30 01:17 bashrc
-rw-r--r--. 2 root root 176 Mar 30 01:17 bashrc_hlink
lrwxrwxrwx. 1 root root   6 Mar 30 16:41 bashrc_slink -> bashrc
#查看属性详情，注意文件权限、文件链接数、文件大小、文件时间差异
[root@localhost tmp]# ls -l --full-time bashrc*
-rw-r--r--. 2 root root 176 2020-03-30 01:17:23.255914128 +0800 bashrc
-rw-r--r--. 2 root root 176 2020-03-30 01:17:23.255914128 +0800 bashrc_hlink
lrwxrwxrwx. 1 root root   6 2020-03-30 16:41:04.928965131 +0800 bashrc_slink -> bashrc



[root@localhost tmp]# tail -5 bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
	. /etc/bashrc
fi



[root@localhost tmp]# pwd
/tmp
[root@localhost tmp]# cp ~/.bashrc /tmp/bashrc
cp: overwrite ‘/tmp/bashrc’? N
#参数 -u 指定 destination 比 source 旧才更新 destination,或 destination 不存在的情况下才复制
[root@localhost tmp]# cp -u ~/.bashrc /tmp/bashrc
[root@localhost tmp]# ls -l --full-time /tmp/bashrc
-rw-r--r--. 2 root root 176 2020-03-30 01:17:23.255914128 +0800 /tmp/bashrc
[root@localhost tmp]# pwd
/tmp
[root@localhost tmp]# cp bashrc_slink bashrc_slink_1
#参数 -d 若来源文件为链接文件属性（link file）,则复制链接文件属性而非文件本身
[root@localhost tmp]# cp -d bashrc_slink bashrc_slink_2
[root@localhost tmp]# ls -l bashrc_slink*
lrwxrwxrwx. 1 root root   6 Mar 30 16:41 bashrc_slink -> bashrc
-rw-r--r--. 1 root root 176 Mar 30 16:54 bashrc_slink_1
lrwxrwxrwx. 1 root root   6 Mar 30 16:55 bashrc_slink_2 -> bashrc
[root@localhost tmp]# ls -l bashrc*
-rw-r--r--. 2 root root 176 Mar 30 01:17 bashrc
-rw-r--r--. 2 root root 176 Mar 30 01:17 bashrc_hlink
lrwxrwxrwx. 1 root root   6 Mar 30 16:41 bashrc_slink -> bashrc
-rw-r--r--. 1 root root 176 Mar 30 16:54 bashrc_slink_1
lrwxrwxrwx. 1 root root   6 Mar 30 16:55 bashrc_slink_2 -> bashrc
      
[root@localhost tmp]# cp ~/.bashrc ~/.bash_history  /tmp
[root@localhost tmp]# ls *bash*
bashrc  bashrc_hlink  bashrc_slink  bashrc_slink_1  bashrc_slink_2
[root@localhost tmp]# ls -al *bash*
-rw-r--r--. 2 root root 176 Mar 30 01:17 bashrc
-rw-r--r--. 2 root root 176 Mar 30 01:17 bashrc_hlink
lrwxrwxrwx. 1 root root   6 Mar 30 16:41 bashrc_slink -> bashrc
-rw-r--r--. 1 root root 176 Mar 30 16:54 bashrc_slink_1
lrwxrwxrwx. 1 root root   6 Mar 30 16:55 bashrc_slink_2 -> bashrc
[root@localhost tmp]# ls -al *bash* .bash*
-rw-------. 1 root root 2774 Mar 30 16:59 .bash_history
-rw-r--r--. 2 root root  176 Mar 30 01:17 bashrc
-rw-r--r--. 1 root root  176 Mar 30 16:59 .bashrc
-rw-r--r--. 2 root root  176 Mar 30 01:17 bashrc_hlink
lrwxrwxrwx. 1 root root    6 Mar 30 16:41 bashrc_slink -> bashrc
-rw-r--r--. 1 root root  176 Mar 30 16:54 bashrc_slink_1
lrwxrwxrwx. 1 root root    6 Mar 30 16:55 bashrc_slink_2 -> bashrc

