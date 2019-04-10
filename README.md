# [canvas-demos API演示](https://kartwutian.github.io/canvas-demos/)

<div id="arc-body">find 是 <a href="/linux_tutorial/" target="_blank">Linux</a> 中强大的搜索命令，不仅可以按照文件名搜索文件，还可以按照权限、大小、时间、inode 号等来搜索文件。但是 find 命令是直接在硬盘中进行搜索的，如果指定的搜索范围过大，find命令就会消耗较大的系统资源，导致服务器压力过大。所以，在使用 find 命令搜索时，不要指定过大的搜索范围。<br>
<br>
find 命令的基本信息如下：
<ul>
<li>
命令名称：find。</li>
<li>
英文原意：search for files in a directory hierarchy.</li>
<li>
所在路径：/bin/find。</li>
<li>
执行权限：所有用户。</li>
<li>
功能描述：在目录中查找文件。</li>
</ul>
<h2>
命令格式</h2>
<p class="info-box">
[root@localhost ~]# find 搜索路径 [选项] 搜索内容</p>
find 是比较特殊的命令，它有两个参数：
<ul>
<li>
第一个参数用来指定搜索路径；</li>
<li>
第二个参数用来指定搜索内容。</li>
</ul>
<br>
而且find命令的选项比较复杂，我们一个一个举例来看。<br>
<h2>
按照文件名搜索</h2>
<p class="info-box">
[root@localhost ~]#find 搜索路径 [选项] 搜索内容</p>
选项：
<ul>
<li>
-name: 按照文件名搜索；</li>
<li>
-iname: 按照文件名搜索，不区分文件名大小；</li>
<li>
-inum: 按照 inode 号搜索；</li>
</ul>
<br>
这是 find 最常用的用法，我们来试试：
<p class="info-box">
[root@localhost ~]# find /-name yum.conf<br>
/etc/yum.conf<br>
#在目录下査找文件名是yum.conf的文件</p>
<br>
但是 find 命令有一个小特性，就是搜索的文件名必须和你的搜索内容一致才能找到。如果只包含搜索内容，则不会找到。我们做一个实验：<br>
<p class="info-box">
[root@localhost ~]# touch yum.conf.bak<br>
#在/root/目录下建立一个文件yum.conf.bak<br>
[root@localhost ~]# find /-name yum.conf<br>
/etc/yum.conf<br>
#搜索只能找到yum.conf文件，而不能找到 yum.conf.bak 文件</p>
find 能够找到的是只有和搜索内容 yum.conf 一致的 /etc/yum.conf 文件，而 /root/yum.conf.bak 文件虽然含有搜索关键字，但是不会被找到。这种特性我们总结为：<span style="color:#b22222;">find 命令是完全匹配的，必须和搜索关键字一模一样才会列出。</span><br>
<br>
Linux 中的文件名是区分大小写的，也就是说，搜索小写文件，是找不到大写文件的。如果想要大小通吃，就要使用 -iname 来搜索文件。<br>
<p class="info-box">
[root@localhost ~]# touch CANGLS<br>
[root@localhost ~]# touch cangls<br>
#建立大写和小写文件<br>
[root@localhost ~]#find.-iname cangls<br>
./CANGLS<br>
./cangls<br>
#使用-iname，大小写文件通吃</p>
每个文件都有 inode 号，如果我们知道 inode 号，则也可以按照 inode 号来搜索文件。<br>
<p class="info-box">
[root@localhost ~]#ls -i install.log<br>
262147 install.log<br>
#如果知道文件名，则可以用"ls -i"来査找inode号<br>
[root@localhost ~]# find.-inum 262147<br>
./install.log<br>
#如果知道inode号，则可以用find命令来査找文件</p>
按照 inode 号搜索文件，也是区分硬链接文件的重要手段，因为硬链接文件的 inode 号是一致的。
<p class="info-box">
[root@localhost ~]# ln /root/install.log /tmp/<br>
#给install.log文件创建一个硬链接文件<br>
[root@localhost ~]#ll -i /root/install.log /tmp/install.log<br>
262147 -rw-r--r--.2 root root 24772 1 月 14 2014/root/<br>
install.log<br>
262147 -rw-r--r--.2 root root 24772 1 月 14 2014/tmp/<br>
install.log<br>
#可以看到这两个硬链接文件的inode号是一致的<br>
[root@localhost ~]# find /-inum 262147<br>
/root/install.log<br>
/tmp/install.log<br>
#如果硬链接不是我们自己建立的，则可以通过find命令搜索inode号，来确定硬链接文件</p>
<h2>
按照文件大小搜索</h2>
<p class="info-box">
[root@localhost ~]#find 搜索路径 [选项] 搜索内容</p>
选项：
<ul>
<li>
-size[+-]大小：按照指定大小搜索文件</li>
</ul>
<br>
这里的"+"的意思是搜索比指定大小还要大的文件，"-" 的意思是搜索比指定大小还要小的文件。我们来试试：<br>
<p class="info-box">
[root@localhost ~]# ll -h install.log<br>
-rw-r--r--.1 root root 25K 1月 14 2014 install.log #在当前目录下有一个大小是25KB的文件<br>
[root@localhost ~]#<br>
[root@localhost ~]# find.-size 25k<br>
./install.log<br>
#当前目录下，査找大小刚好是25KB的文件，可以找到<br>
[root@localhost ~]# find .-size -25k<br>
.<br>
./.bashrc<br>
./.viminfo<br>
./.tcshrc<br>
./.pearrc<br>
./anaconda-ks.cfg<br>
./test2<br>
./.ssh<br>
./.bash_history<br>
./.lesshst<br>
./.bash_profile<br>
./yum.conf.bak<br>
./.bashjogout<br>
./install.log.syslog<br>
./.cshrc<br>
./cangls<br>
#搜索小于25KB的文件，可以找到很多文件<br>
[root@localhost ~]# find.-size +25k<br>
#而当前目录下没有大于25KB的文件</p>
<br>
其实 find 命令的 -size 选项是笔者个人觉得比较恶心的选项，为什么这样说？find 命令可以按照 KB 来搜索，应该也可以按照 MB 来搜索吧。
<p class="info-box">
[root@localhost ~]# find.-size -25m<br>
find:无效的-size类型"m"<br>
#为什么会报错呢？其实是因为如果按照MB来搜索，则必须是大写的M</p>
这就是纠结点，千字节必须是小写的"k"，而兆字节必领是大写的"M"。有些人会说："你别那么执着啊，你就不能不写单位，直接按照字节搜索啊？"很傻，很天真，不写单位，你们就以为会按照字节搜索吗？我们来试试：<br>
<p class="info-box">
[root@localhost ~]# ll anaconda-ks.cfg<br>
-rw-------.1 root root 1207 1 月 14 2014 anaconda-ks.cfg<br>
#anaconda-ks.cfg文件有1207字芳<br>
[root@localhost ~]# find.-size 1207<br>
#但用find查找1207，是什么也找不到的</p>
也就是说，find 命令的默认单位不是字节。如果不写单位，那么 find 命令是按照 512 Byte 来进行査找的。 我们看看 find 命令的帮助。
<p class="info-box">
[root@localhost ~]# man find<br>
-size n[cwbkMG]<br>
File uses n units of space. The following suffixes can be used:<br>
'b' for 512-byte blocks (this is the default if no suffix is used)<br>
#这是默认单位，如果单位为b或不写单位，则按照 512Byte搜索<br>
'c' for bytes<br>
#搜索单位是c，按照字节搜索<br>
'w' for two-byte words<br>
#搜索单位是w，按照双字节（中文）搜索<br>
'k'for Kilobytes (units of 1024 bytes)<br>
#按照KB单位搜索，必须是小写的k<br>
'M' for Megabytes (units of 1048576 bytes)<br>
#按照MB单位搜索，必须是大写的M<br>
'G' for Gigabytes (units of 1073741824 bytes)<br>
#按照GB单位搜索，必须是大写的G</p>
也就是说，如果想要按照字节搜索，则需要加搜索单位"c"。我们来试试：
<p class="info-box">
[root@localhost ~]# find.-size 1207c<br>
./anaconda-ks.cfg<br>
#使用搜索单位c，才会按照字节搜索</p>
<h2>
按照修改时间搜索</h2>
Linux 中的文件有访问时间(atime)、数据修改时间(mtime)、状态修改时间(ctime)这三个时间，我们也可以按照时间来搜索文件。<br>
<p class="info-box">
[root@localhost ~]# find搜索路径 [选项] 搜索内容</p>
选项：<br>
<ul>
<li>
-atime [+-]时间: 按照文件访问时间搜索</li>
<li>
-mtime [+-]时间: 按照文改时间搜索</li>
<li>
-ctime [+-]时间: 按照文件修改时间搜索</li>
</ul>
这三个时间的区别我们在 stat 命令中已经解释过了，这里用 mtime 数据修改时间来举例，重点说说 "[+-]"时间的含义。
<ul>
<li>
-5：代表@内修改的文件。</li>
<li>
5：代表前5~6天那一天修改的文件。</li>
<li>
+5：代表6天前修改的文件。</li>
</ul>
我们画一个时间轴，来解释一下，如图 1 所示。<br>
<div style="text-align: center;">
<br>
<img alt="" src="/uploads/allimg/180930/2-1P930143J9411.jpg"><br>
图 1 find时间轴</div>
<br>
每次笔者讲到这里，"-5"代表 5 天内修改的文件，而"+5"总有人说代表 5 天修改的文件。要是笔者能知道 5 天系统中能建立什么文件，早就去买彩票了，那是未卜先知啊！所以"-5"指的是 5 天内修改的文件，"5"指的是前 5~6 天那一天修改的文件，"+5"指的是 6 天前修改的文件。我们来试试：
<p class="info-box">
[root@localhost ~]#find.-mtime -5<br>
#查找5天内修改的文件</p>
大家可以在系统中把几个选项都试试，就可以明白各选项之间的差别了。<br>
<br>
find 不仅可以按照 atmie、mtime、ctime 来査找文件的时间，也可以按照 amin、mmin 和 cmin 来査找文件的时间，区别只是所有 time 选项的默认单位是天，而 min 选项的默认单位是分钟。<br>
<h2>
按照权限搜索</h2>
在 find 中，也可以按照文件的权限来进行搜索。权限也支持 [+/-] 选项。我们先看一下命令格式。<br>
<p class="info-box">
[root@localhost ~]# find 搜索路径 [选项] 搜索内容</p>
选项：<br>
<ul>
<li>
-perm 权限模式：査找文件权限刚好等于"权限模式"的文件</li>
<li>
-perm -权限模式：査找文件权限全部包含"权限模式"的文件</li>
<li>
-perm +权限模式：査找文件权限包含"权限模式"的任意一个权限的文件</li>
</ul>
<br>
为了便于理解，我们要举几个例子。先建立几个测试文件。<br>
<p class="info-box">
[root@localhost ~]# mkdir test<br>
[root@localhost ~]# cd test/<br>
[root@localhost test]# touch testl<br>
[root@localhost test]# touch test2<br>
[root@localhost test]# touch test3<br>
[root@localhost test]# touch test4<br>
#建立测试目录，以及测试文件<br>
[root@localhost test]# chmod 755 testl<br>
[root@localhost test]# chmod 444 test2<br>
[root@localhost test]# chmod 600 test3<br>
[root@localhost test]# chmod 200 test4<br>
#设定实验权限。因为是实验权限，所以看起来比较别扭<br>
[root@localhost test]# ll<br>
总用量0<br>
-rwxr-xr-x 1 root root 0 6月 17 11:05 testl -r--r--r-- 1 root root 0 6月 17 11:05 test2<br>
-rw------- 1 root root 0 6月 17 11:05 test3<br>
-w------- 1 root root 0 6月 17 11:05 test4<br>
#查看权限</p>
<br>
<strong>【例 1】</strong>"-perm权限模式"。<br>
这种搜索比较简单，代表査找的权限必须和指定的权限模式一模一样，才可以找到。<br>
<p class="info-box">
[root@localhost test]#find.-perm 444<br>
./test2<br>
[root@localhost test]#find.-perm 200<br>
./test4<br>
#按照指定权限搜索文件，文件的权限必须和搜索指定的权限一致，才能找到</p>
<br>
<strong>【例 2】</strong>"-perm-权限模式"。<br>
如果使用"-权限模式"，是代表的是文件的权限必须全部包含搜索命令指定的权限模式，才可以找到。
<p class="info-box">
[root@localhost test]#find .-perm -200<br>
./test4 &lt;-此文件权限为200<br>
./test3 &lt;-此文件权限为600<br>
./testl &lt;-此文件权限为755<br>
#搜索文件的权限包含200的文件，不会找到test2文件，因为test2的权限为444，不包含200权限</p>
因为 test4 的权限 200(-w-------)、test3 的权限 600(-rw-------)和 test1 的权限 755(-rwxr-xr-x) 都包含 200(--w-------) 权限，所以可以找到；而 test2 的权限是 444 (-r--r--r--)，不包含 200 (--w-------)权限，所以找不到，再试试：
<p class="info-box">
[root@localhost test]# find .-perm -444<br>
.<br>
./test2 &lt;-此文件权限为444<br>
./test1 &lt;-此文件权限为755<br>
#搜索文件的权限包含444的文件</p>
上述搜索会找到 test1 和 test2，因为 test1 的权限 755 (-rwxr-xr-x)和 test2 的权限 444 (-r--r--r--)都完全包含 444 (-r--r--r--)权限，所以可以找到；而 test3 的权限 600 (-rw-------)和 test4 的权限 200 (-w-------)不完全包含 444 (-r--r--r--) 权限，所以找不到。也就是说，test3 和 test4 文件的所有者权限虽然包含 4 权限，但是所属组权限和其他人权限都是 0，不包含 4 权限，所以找不到，这也是完全包含的意义。<br>
<br>
<strong>【例 3】</strong>"-perm+权限模式"<br>
刚刚的"-perm-权限模式"是必须完全包含，才能找到；而"-perm+权限模式"是只要包含任意一个指定权限，就可以找到。我们来试试：
<p class="info-box">
[root@localhost test]# find .-perm +444<br>
./test4 &lt;-此文件权限为200<br>
./test3 &lt;-此文件权限为600<br>
./testl &lt;-此文件权限为755<br>
#搜索文件的权限包含200的文件，不会找到test2文件，因为test2的权限为444，不包含200权限。</p>
因为 test4 的权限 200 (--w-------)、test3 的权限 600 (-rw-------)和 test1 的权限 755 (-rwxr-xr-x)都包含 200(--w-------)权限，所以可以找到；而 test2 的权限是 444 (-r--r--r--)，不包含 200 (--w-------)权限，所以找不到。<br>
<h2>
按照所有者和所属组搜索</h2>
<p class="info-box">
[root@localhost ~]# find 搜索路径 [选项] 搜索内容</p>
选项：<br>
<ul>
<li>
-uid 用户 ID:按照用户 ID 査找所有者是指定 ID 的文件</li>
<li>
-gid 组 ID:按照用户组 ID 査找所属组是指定 ID 的文件</li>
<li>
-user 用户名：按照用户名査找所有者是指定用户的文件</li>
<li>
-group 组名：按照组名査找所属组是指定用户组的文件</li>
<li>
-nouser：査找没有所有者的文件</li>
</ul>
<br>
这组选项比较简单，就是按照文件的所有者和所属组来进行文件的査找。在 Linux 系统中，绝大多数文件都是使用 root 用户身份建立的，所以在默认情况下，绝大多数系统文件的所有者都是 root。例如：
<p class="info-box">
[root@localhost ~]#find.-user root<br>
#在当前目录中査找所有者是 root 的文件</p>
由于当前目录是 root 的家目录，所有文件的所有者都是 root 用户，所以这条搜索命令会找到当前目录下所有的文件。<br>
<br>
按照所有者和所属组搜索时，"-nouser"选项比较常用，主要用于査找垃圾文件。在 Linux 中，所有的文件都有所有者，只有一种情况例外，那就是外来文件。比如光盘和 U 盘中的文件如果是由 Windows 复制的，在 Linux 中査看就是没有所有者的文件；再比如手工源码包安装的文件，也有可能没有所有者。<br>
<br>
除这种外来文件外，如果系统中发现了没有所有者的文件，一般是没有作用的垃圾文件（比如用户删除之后遗留的文件），这时需要用户手工处理。搜索没有所有者的文件，可以执行以下命令：<br>
<p class="info-box">
[root@localhost ~]# find/-nouser</p>
<h2>
按照文件类型搜索</h2>
<p class="info-box">
[root@localhost ~]# find 搜索路径 [选项] 搜索内容</p>
选项:<br>
<ul>
<li>
-type d：查找目录</li>
<li>
-type f：查找普通文件</li>
<li>
-type l：查找软链接文件</li>
</ul>
<br>
这个命令也很简单，主要按照文件类型进行搜索。在一些特殊情况下，比如需要把普通文件和目录文件区分开，比如需要把普通文件和目录文件区分开，使用这个选项就很方便。<br>
<p class="info-box">
[root@localhost ~]# find /etc -type d<br>
#查找/etc/目录下有哪些子目录</p>
<h2>
逻辑运算符</h2>
<p class="info-box">
[root@localhost ~]#find 搜索路径 [选项] 搜索内容</p>
选项：
<ul>
<li>
-a：and逻辑与</li>
<li>
-o：or逻辑或</li>
<li>
-not：not逻辑非</li>
</ul>
<h4>
1) -a:and逻辑与</h4>
find 命令也支持逻辑运算符选项，其中 -a 代表逻辑与运算，也就是 -a 的两个条件都成立，find 搜索的结果才成立。<br>
<br>
举个例子：
<p class="info-box">
[root@localhost ~]# find.-size +2k -a -type f<br>
#在当前目录下搜索大于2KB，并且文件类型是普通文件的文件</p>
在这个例子中，文件既要大于 2KB，又必须是普通文件，find 命令才可以找到。再举个例子：<br>
<p class="info-box">
[root@localhost ~]# find.-mtime -3 -a -perm 644<br>
#在当前目录下搜索3天以内修改过，并且权限是644的文件</p>
<h4>
2) -o:or逻辑或</h4>
-o 选项代表逻辑或运算，也就是 -o 的两个条件只要其中一个成立，find 命令就可以找到结果。例如：
<p class="info-box">
[root@localhost ~]#find.-name cangls -o -name bols<br>
./cangls<br>
./bols<br>
#在当前目录下搜索文件名要么是cangls的文件，要么是bols的文件</p>
-o 选项的两个条件只要成立一个，find 命令就可以找到结果，所以这个命令既可以找到 cangls 文件，也可以找到 bols 文件。<br>
<h4>
3) -not:not逻辑非</h4>
<p class="info-box">
-not是逻辑非，也就是取反的意思。举个例子:<br>
[root@localhost ~]# find.-not -name cangls<br>
#在当前目录下搜索文件名不是cangls的文件</p>
<h4>
其他选项</h4>
1) -exec选项<br>
这里我们主要讲解两个选项"-exec"和"-ok"，这两个选项的基本作用非常相似。我们先来看看 "exec"选项的格式。
<p class="info-box">
[root@localhost ~]# find 搜索路径 [选项] 搜索内容 -exec 命令2{}\;</p>
首先，请大家注意这里的"{}"和"\;"是标准格式，只要执行"-exec"选项，这两个符号必须完整输入。<br>
<br>
其次，这个选项的作用其实是把 find 命令的结果交给由"-exec"调用的命令 2 来处理。"{}"就代表 find 命令的査找结果。<br>
<br>
我们举个例子，刚刚在讲权限的时候，使用权限模式搜索只能看到文件名，例如：<br>
<p class="info-box">
[root@localhost test]#find.-perm 444<br>
./test2</p>
如果要看文件的具体权限，还要用"ll"命令査看。用"-exec"选项则可以一条命令搞定：<br>
<p class="info-box">
[root@localhost test]# find.-perm 444 -exec ls -l {}\；<br>
-r--r--r-- 1 root root 0 6月 17 11:05 ./test2<br>
#使用"-exec"选项，把find命令的结果直接交给"ls -l"命令处理</p>
"-exec"选项的作用是把 find 命令的结果放入"{}"中，再由命令 2 直接处理。在这个例子中就是用"ls -l"命令直接处理，会使 find 命令更加方便。<br>
<br>
2) -ok选项<br>
"-ok"选项和"-exec"选项的作用基本一致，区别在于："-exec"的命令会直接处理，而不询问；"-ok"的命令 2 在处理前会先询问用户是否这样处理，在得到确认命令后，才会执行。例如：
<p class="info-box">
[root@localhost test]# find .-perm 444 -ok rm -rf{}\;<br>
&lt;rm…./test2&gt;?y&nbsp; &lt;-需要用户输入y,才会执行<br>
#我们这次使用rm命令来删除find找到的结果，删除的动作最好确认一下</p>
</div>
