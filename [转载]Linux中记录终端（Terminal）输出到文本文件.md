

一，如何把命令运行的结果保存到文件当中?
　　这个问题太简单了，大家都知道，用 > 把输出转向就可以了
　　例子:
　　[lhd@hongdi ~]$ ls > ls.txt
　　[lhd@hongdi ~]$ cat ls.txt
　　1.gtkrc-2.0
　　2009
　　a
　　amsn_received
　　a.tar.gz
　　说明: > 是把输出转向到指定的文件，如文件已存在的话也会重新写入，文件原内容不会保留
　　     >> 是把输出附向到文件的后面，文件原内容会保留下来

二，如何能在输出信息的同时把信息记录到文件中?
　　我们在上面的例子中可以看到，我们使用输出转向，命令在终端上的输出转向到了文件中，但如果我希望能同时在终端上看到输出信息怎么办？
　　我们可以使用这个命令: tee
　　解释一下tee的作用:
　　read from standard input and write to standard output and files
　　它从标准输入读取内容并将其写到标准输出和文件中
　　看例子:
　　[lhd@hongdi ~]$ ls | tee ls_tee.txt
　　1.gtkrc-2.0
　　2009
　　a
　　amsn_received
　　a.tar.gz
　　[lhd@hongdi ~]$ cat ls_tee.txt
　　1.gtkrc-2.0
　　2009
　　a
　　amsn_received
　　a.tar.gz
备注：使用 tee时,如果想保留目标文件原有的内容怎么办？
　　可以使用 -a参数
　　-a, --append
　　append to the given FILEs, do not overwrite
　　附加至给出的文件，而不是覆盖它

三，多个命令的输出都需要记录，可以用script
　　script这个命令很强大，可以记录终端的所有输出到相应的文件中
　　看例子:
　　[lhd@hongdi ~]$ script
　　Script. started, file is typescript
　　[lhd@hongdi ~]$ ls
　　1.gtkrc-2.0 c.tar kmess-2.0alpha2.tar.gz secpanel-0.5.3-1.noarch.rpm
　　2009 DownZipAction.php kmesslog secpanel-0.5.4-2.noarch.rpm
　　[lhd@hongdi ~]$ exit
　　exit
　　Script. done, file is typescript
　　[lhd@hongdi ~]$ cat typescript
　　Script. started on 2009年02月08日 星期日 18时56分52秒
　　[lhd@hongdi ~]$ ls
　　1.gtkrc-2.0 c.tar kmess-2.0alpha2.tar.gz secpanel-0.5.3-1.noarch.rpm
　　2009 DownZipAction.php kmesslog secpanel-0.5.4-2.noarch.rpm
　　[lhd@hongdi ~]$ exit
　　exit
　　Script. done on 2009年02月08日 星期日 18时57分00秒
　　说明:
　　1,我们在启动script时没有指定文件名，它会自动记录到当前目录下一个名为 typescript的文件中。也可以用 -a参数 指定文件名
　　例子:
　　[lhd@hongdi ~]$ script. -a example.txt
　　Script. started, file is example.txt
　　此时终端的输出内容被记录到 example.txt这个文件中
　　2,退出script时，用exit
　　感到奇怪吗？事实上script就是启动了一个shell
　　看一下ps auxfww 的信息就知道了
　　lhd 17738 0.1 3.2 152028 33328 ? Sl 18:30 0:03 /usr/bin/konsole
　　lhd 17740 0.0 0.1 6372 1720 pts/1 Ss 18:30 0:00 _ /bin/bash
　　lhd 17900 0.0 0.0 5344 628 pts/1 S 19:01 0:00 | _ script
　　lhd 17901 0.0 0.0 5348 464 pts/1 S 19:01 0:00 | _ script
　　lhd 17902 0.5 0.1 6372 1688 pts/2 Ss 19:01 0:00 | _ bash -i
　　3,查看typescript的内容，可以看到它同时记录下了script的启动和结束时间

　　四，用script录制并播放session的内容
　　我们可以用 script把整个终端会话的所有操作和输出录制下来，然后再用scriptreplay进行播放。
　　如果录制时记录下来了操作时的时间数据，那么播放时和操作时的使用时间完全相同。
　　这个很有用吧，比如：我们可以把安装软件时编译的过程记录下来，然后给别人进行演示
　　看例子:
　　[lhd@hongdi ~]$ script. -t 2>example.time -a example.txt
　　Script. started, file is example.txt
　　[lhd@hongdi ~]$ ls
　　说明: -t 2>example.time -t是把时间数据输出到标准错误(standard error)，所以我们使用 2>example.time 把数据转向到 example.time这个文件当中
　　如何播放所记录的内容?
　　第一步：安装scriptreplay
　　下载
　 　wget linux/utils/util-linux/util-linux-2.12r.tar.bz2">ftp://ftp.kernel.org/pub/linux/utils/util-linux/util-linux-2.12r.tar.bz2
　　解压
　　tar -jxvf util-linux-2.12r.tar.bz2
　　之后复制文件到系统的命令目录中即可
　　[root@hongdi 下载]# cp util-linux-2.12r/misc-utils/scriptreplay.pl /usr/bin/scriptreplay
　　[root@hongdi 下载]# chmod 755 /usr/bin/scriptreplay
　　备注: fedora 10的util-linux-ng-2.14.1-3.2.fc10.i386.rpm 此包中已包含 scriptreplay,已无需另行安装
　　第二步：播放所录制的session内容
　　[lhd@hongdi ~]$ scriptreplay example1.time example1.txt
　　[lhd@hongdi ~]$ ls
　　1.gtkrc-2.0 c.tar jeffray_lee@hotmail.com pass
　　[lhd@hongdi ~]$ abcd
　　bash: abcd: command not found

　　[lhd@hongdi ~]$ exit
