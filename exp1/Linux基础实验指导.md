# Linux基础实验指导

## 一、实验目的

- 通过对常用命令cd、ls、cp、mv、rm、mkdir等文件命令的操作，掌握Linux操作系统中文件命令的用法。

- 了解集群中多主机之间的文件传输的命令。

## 二、实验任务与要求

- 使用ssh指令登录远程服务器，并设置免密登录。
- 掌握mkdir、cd、pwd命令的操作，要求能建立目录、进入与退出目录。
- 掌握cp、ls、mv、rm命令的操作，要求能拷贝文件、新建文件、查看文件、文件重命名、删除文件等操作。
- 掌握vi或vim的使用，要求能新建、编辑、保存一个文本文件。
- 掌握cat、head、scp、awk、grep等文本处理命令的使用，对文本数据进行查看、过滤、统计等操作。
- 了解Linux中阻塞与非阻塞的概念，测试两种情况指令执行的情况。
- 配置集群中多主机的免密登录，掌握使用scp在不同主机之间传输文件。
- 尝试在多节点（即多个Linux主机上）上完成你的任务，包括如何将资源分配至各个节点、如何协调各个节点的任务、如何整合多个结果等等。对比在单节点和多节点上任务处理的速度。
- （加分项）尝试提出一种可以加快多节点处理速度的方法。

## 三、实验步骤

#### 任务1. 使用ssh远程登录服务器

课程服务器的地址是**szcluster.mmlab.top**，使用ssh xxx@szcluster.mmlab.top命令即可登录服务器，其中**xxx**使用大家的学号代替, 对应的密码也是学号。

```
$ ssh 2018211009@szcluster.mmlab.top
2018211009@szcluster.mmlab.top's password:
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.12.9-041209-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

2018211009@thumm01:~$
```

#### 任务2. 配置免密登录

接下来我们需要配置一下免密登录。任务1中每次登录需要输入密码挺麻烦的, 对此我们可以配置免密登录，原理是将本机的公钥保存在服务器，每次登录时主机和服务器可以通过公钥验证身份，因此不再需要输入密码。下面是免密登录的方法:

##### 2.1. 生成公钥和私钥

使用ssh-keygen生成公钥和私钥，存放的位置一般不需要改。

```
2018211009@thumm01:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/dsjxtjc/2018211009/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/dsjxtjc/2018211009/.ssh/id_rsa.
Your public key has been saved in /home/dsjxtjc/2018211009/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:pDlFzmQA+bFtVlcSwH3hqMT9Du/qjs7rMu7eXb9yZls 2018211009@thumm01
The key's randomart image is:
+---[RSA 2048]----+
|    .o..+..oooo. |
|    . .* .o.o+.  |
|     . +=.o.o..  |
|      o=+. . .   |
|      +oS . . .  |
|       .     +   |
|              + E|
|        oo o + =.|
|       ++=B+=.*o+|
+----[SHA256]-----+
```

##### 2.2. 将公钥内容复制到服务器

使用ssh-copy-id将本地的公钥(localhost:\~/.ssh/id_rsa.pub)添加到远程服务器的认证列表(szcluster.mmlab.top:\~/.ssh/authorized_keys)中, 这个过程也可以手动拷贝。

```
2018211009@thumm01$ ssh-copy-id 2018211009@thumm02
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/dsjxtjc/2018211009/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
2018211009@thumm02's password:

Number of key(s) added:        1

Now try logging into the machine, with:   "ssh '2018211009@thumm02'"
and check to make sure that only the key(s) you wanted were added.
```

之后使用ssh登录服务器就不需要输入密码了~

<div STYLE="page-break-after: always;"></div> 

#### 任务3. 掌握pwd、mkdir、cd命令的操作，要求能建立目录、进入与退出目录

查看当前目录

```
2018211009@thumm01:~$ pwd
/home/dsjxtjc/2018211009
```

创建新目录

```
2018211009@thumm01:~$ mkdir dir_name
2018211009@thumm01:~$ ls
dir_name
```

进入新目录

```
2018211009@thumm01:~$ cd dir_name
2018211009@thumm01:~/dir_name$ pwd
/home/dsjxtjc/2018211009/dir_name
```

退出回到上级目录

```
2018211009@thumm01:~/dir_name$ cd ..
2018211009@thumm01:~$ pwd
/home/dsjxtjc/2018211009
```

#### 任务4. 掌握cp、vim、ls、mv、rm命令的操作，要求能拷贝文件、新建文件、查看文件、文件重命名、删除文件等操作

使用vim创建一个文件file.txt, **在命令模式下输入i 切换到插入模式**，输入内容‘helloworld’, **按ESC返回命令模式**，输入:wq保存并退出。

```
2018211009@thumm01:~$ vim file.txt
2018211009@thumm01:~$ ls
dir_name  file.txt
```

查看文件内容

```
2018211009@thumm01:~$ cat file.txt
helloworld
```

拷贝文件file.txt, 生成新的文件new_file.txt

```
2018211009@thumm01:~$ cp file.txt new_file.txt
2018211009@thumm01:~$ ls
dir_name  file.txt  new_file.txt
```

<div STYLE="page-break-after: always;"></div> 

给新文件重命名

```
2018211009@thumm01:~$ mv new_file.txt new_file_renamed.txt
2018211009@thumm01:~$ ls
dir_name  file.txt  new_file_renamed.txt
```

删除file.txt

```
2018211009@thumm01:~$ rm file.txt
2018211009@thumm01:~$ ls
dir_name  new_file_renamed.txt
```

查看文件详细信息

```
2018211009@thumm01:~$ ls -l
total 8
drwxr-xr-x 2 2018211009 dsjxtjc 4096 Sep 20 16:02 dir_name
-rw-r--r-- 1 2018211009 dsjxtjc   11 Sep 20 16:12 new_file_renamed.txt
```

#### 任务5. 掌握cat、head、scp、awk、grep等文本处理命令的使用，对文本数据进行查看、过滤、统计等操作

首先拷贝数据集wc_dataset.txt到主目录下

```
2018211009@thumm01:~$ cp /home/dsjxtjc/wc_dataset.txt ./
2018211009@thumm01:~$ ls
dir_name  new_file_renamed.txt	wc_dataset.txt
```

接着使用指令对该数据集进行操作

##### head、tail命令使用

head/tail用于查看文件头部/尾部的内容，默认最多显示十行

```
2018211009@thumm01:~$ head wc_dataset.txt
chapter
i
down
the
rabbit
hole
alice
was
beginning
to
2018211009@thumm01:~$
```

<div STYLE="page-break-after: always;"></div> 

也可以通过添加参数-n来设定显示的行数

```
2018211009@thumm01:~$ head -n 5 wc_dataset.txt
chapter
i
down
the
rabbit
2018211009@thumm01:~$
```

head和tail可以结合，可以查看文件中任意几行的内容。例如我们要查看wc_dataset.txt中6-10行，我们可以这样做

```
2018211009@thumm01:~$ head -n 10 wc_dataset.txt | tail -n 5
hole
alice
was
beginning
to
2018211009@thumm01:~$
```

##### 重定向符'>'的使用

重定向符可以将指令执行的结果重新定向，可以将原本在控制台输出的内容输出到文件。

将wc_dataset.txt中1-5行内容保存为文件wc_1-5.txt, 将6-10行保存为wc_6-10.txt。

```
2018211009@thumm01:~$ head -n 5 wc_dataset.txt > wc_1-5.txt
2018211009@thumm01:~$ head -n 10 wc_dataset.txt | tail -n 5 > wc_6-10.txt
2018211009@thumm01:~$ ls
dir_name  new_file_renamed.txt	wc_1-5.txt  wc_6-10.txt  wc_dataset.txt
```

使用了重定向符，原先执行指令后的结果输出不见了，同时可以看到多了wc_1-5.txt和wc_6-10.txt两个文件，指令的输出结果被保存在了文件中。

##### cat命令使用

查看两文件的内容

```
2018211009@thumm01:~$ cat wc_1-5.txt
chapter
i
down
the
rabbit
2018211009@thumm01:~$ cat wc_6-10.txt
hole
alice
was
beginning
to
```

<div STYLE="page-break-after: always;"></div> 

cat 也可以同时查看多个文件内容, *cat wc_1-5.txt wc_6-10.txt > wc_1-10.txt* 相当于合并两文件，内容为1-10行的内容。

```
2018211009@thumm01:~$ cat wc_1-5.txt wc_6-10.txt > wc_1-10.txt
2018211009@thumm01:~$ cat wc_1-10.txt
chapter
i
down
the
rabbit
hole
alice
was
beginning
to
2018211009@thumm01:~$ head -n 10 wc_dataset.txt
chapter
i
down
the
rabbit
hole
alice
was
beginning
to
```

可以看到结果和head -n 10 wc_dataset.txt一致。

- scp命令使用

scp命令用来在不同主机之间传输文件，它使用的协议是SSH协议，两台主机之间若配置好了ssh免密，那么使用scp传输时不需要输入密码。
scp的详细用法可以通过man指令查看, 下面简单介绍下不同主机之间文件的传输。

首先开启两个终端，一个终端连接上thumm01, 另一个连接连接上thumm02(这里开两个终端只是为了方便查看实验的结果，scp并不需要预先登录两台主机)

在thumm01上

```
2018211009@thumm01:~$ ls
dir_name  new_file_renamed.txt	wc_1-10.txt  wc_1-5.txt  wc_6-10.txt  wc_dataset.txt
2018211009@thumm01:~$
```

在thumm02上

```
2018211009@thumm02:~$ ls
2018211009@thumm02:~$
```

将thumm01中的wc_1-10.txt传到thumm02的主目录

<div STYLE="page-break-after: always;"></div> 

在thumm01上

```
2018211009@thumm01:~$ scp wc_1-10.txt thumm02:~/
2018211009@thumm02's password:
wc_1-10.txt                      100%   54     0.1KB/s   00:00
```

在thumm02上多了一个wc_1-10.txt

```
2018211009@thumm02:~$ ls
wc_1-10.txt
```

##### awk命令使用

awk是一种处理文本文件的语言，是一个强大的文本分析工具，它的具体介绍可以看 [Linux grep 命令](https://www.runoob.com/linux/linux-comm-awk.html)，我们仅介绍awk的一些常用的功能。

基本用法
```sh
awk [选项参数] 'script' var=value file(s)
或
awk [选项参数] -f scriptfile var=value file(s)
```

awk指令适合处理格式规整的数据，例如/etc/passwd文件，它保存着Linux系统中用户的用户名以及其他信息(不包含密码), 我们可以通过它了解当前主机上的用户信息，例如我将使用awk查看服务器用户列表中学号为2019开头的学生的个数。

要处理数据，我们首先要分析一下数据的格式

```
2018211009@thumm01:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
......
2019211333:x:1057:502::/home/dsjxtjc/2019211333:/bin/bash
2019211334:x:1058:502::/home/dsjxtjc/2019211334:/bin/bash
```

我们可以看到，数据每一行代表一个用户，开头为用户的用户名，后面为用户信息（具体代表什么我们不管），每个信息使用‘:’进行分割。对此，我们可以依据逗号进行分割，然后取每行第一个元素（用户名），使用正则表达式匹配下看用户名是否为2019开头，如果是则输出。

要实现这个功能，我们可以使用下面的指令

```
2018211009@thumm01:~$ awk -F: '$1~"^2019"{print $1}' /etc/passwd
2019211259
2019211315
......
2019211334
```

其中-F:参数表示使用:作为分隔符进行分割，\$1~"^2019"{print \$1}中\$1表示分割后第一个元素（用户名），"\^2019"是一个正则表达式，表示以2019开头，‘~’表示匹配，所以\$1\~"^2019"表示分割后第一个元素满足2019开头，那么就执行后面的指令{print \$1}。
所以指令最终会输出所有2019开头的用户名。

<div STYLE="page-break-after: always;"></div> 

使用wc -l统计下有多少个这样的学号，即可知道参加本课程的19级同学的数量了。

```
2018211009@thumm01:~$ awk -F: '$1~"^2019"{print $1}' /etc/passwd | wc -l
49
```

可以看到，一共有49名19级同学参加本课程~~

#####  grep命令使用

> grep命令用于查找文件里符合条件的字符串。grep 指令用于查找内容包含指定的范本样式的文件，如果发现某文件的内容符合所指定的范本样式，预设 grep 指令会把含有范本样式的那一列显示出来。若不指定任何文件名称，或是所给予的文件名为-，则 grep 指令会从标准输入设备读取数据。

接下来我们使用grep命令对wc_dataset.txt作分析

1. 显示1000~2000行中所有以"dis"开头的单词（显示前10条）

```
2018211009@thumm01:~$ grep "^dis" wc_dataset.txt |head
disappointment
distance
disagree
distance
distance
distance
distant
dish
dishes
disgust
```

2. 反向过滤，添加参数-v。

查找wc_1-10.txt中以t字母开头的单词：

```
2018211009@thumm01:~$ grep "^t" wc_1-10.txt
the
to
```

接着添加参数-v，过滤掉wc_0-9.txt以t开头的单词。

```
2018211009@thumm01:~$ grep -v "^t" wc_1-10.txt
chapter
i
down
rabbit
hole
alice
was
beginning
```

<div STYLE="page-break-after: always;"></div> 

#### 任务6. 阻塞与非阻塞时间对比

在Linux shell脚本中，里面的指令是顺序执行的，但实际上一些之间并没有依赖关系，这些没有相互依赖（或者说数据关联）的指令可以并行地运行而对结果没有影响。为了让同一个脚本中没有相互依赖的指令并行地执行，我们就需要在边写shell脚本时指定这些指令为非阻塞。

让一条指令不阻塞的方法是在指令德最后面添加上'&'。

接下来边写两个脚本，第一个脚本使用阻塞的方法执行，第二个脚本使用非阻塞的方法执行，对比两个脚本的运行时间：

脚本一: shell_blocked.sh

```sh
#!/bin/bash
awk '$1~"^chapter"{}' wc_dataset.txt
awk '$1~"^chapter"{}' wc_dataset.txt
awk '$1~"^chapter"{}' wc_dataset.txt
awk '$1~"^chapter"{}' wc_dataset.txt
awk '$1~"^chapter"{}' wc_dataset.txt
```

脚本二：shell_unblocked.sh

```sh
#!/bin/bash
awk '$1~"^chapter"{}' wc_dataset.txt &
awk '$1~"^chapter"{}' wc_dataset.txt &
awk '$1~"^chapter"{}' wc_dataset.txt &
awk '$1~"^chapter"{}' wc_dataset.txt &
awk '$1~"^chapter"{}' wc_dataset.txt &
wait
```

运行这两个脚本，对比它们运行的时间

```
2018211009@thumm01:~$ vim shell_block.sh
2018211009@thumm01:~$ vim shell_unblock.sh
2018211009@thumm01:~$ time bash ./shell_block.sh

real	0m5.387s
user	0m5.323s
sys	0m0.064s

2018211009@thumm01:~$ time bash ./shell_unblock.sh

real	0m1.142s
user	0m5.521s
sys	0m0.025s
```

运行程序后，可以看到用户时间不变，都是5秒左右，即所耗费的计算资源不变，但是真实时间脚本二是脚本一的五分之一，因此从用户的角度看脚本二运行更快。
（用户时间user time是指程序在多个核上运行时间的和，真实时间real time是现实中程序运行过去了多长时间，真实时间变短原因是每个操作不再阻塞，而是利用多个处理器核心并行计算。）

<div STYLE="page-break-after: always;"></div> 

#### 任务7. 多节点任务处理


为了充分利用集群的运算性能，我们需要将资源分配至各个节点、协调各个节点的任务、整合多个结果等等。接下来我们来控制命令在多个主机上协同运行。

##### 7.1 集群主机之间免密登录配置

首先，我们需要在这些节点之间配置免密登录，下面是一个写好的设置集群中主机之间免密登录的脚本，首先创建一个ssh-keys的文件夹

```
2018211009@thumm01:~$ mkdir ssh-keys
2018211009@thumm01:~$ cd ssh-keys
```

创建一个登录脚本auto_auth.sh, 内容如下
```
2018211009@thumm01:~/ssh-keys$ vim auto_auth.sh
```

auto_autho.sh文件的内容:

```sh
#!/bin/bash
echo "" > authorized_keys
for ((i=1; i<=5; i=i+1));do
    mkdir -p thumm0$i
    ssh-keygen -q -t rsa -N "" -f thumm0$i/id_rsa
    cat thumm0$i/id_rsa.pub >> authorized_keys
done
for ((i=1; i<=5; i=i+1));do
    cp authorized_keys thumm0$i/
    ssh thumm0$i "mkdir -p ~/.ssh"
    scp -r thumm0$i/* thumm0$i:~/.ssh/
done
```
这个脚本做的事情是现在thumm01上生成5个节点的公钥和私钥，然后把所有公钥加入到authorized_keys中，然后把各自的公钥私钥以及authorized_keys分发到各个节点。

运行auto_auth.sh, 这里需要输入8次密码

```
2018211009@thumm01:~/ssh-keys$ bash ./auto_auth.sh
authorized_keys                              100% 2001     2.0KB/s   00:00
id_rsa                                       100% 1675     1.6KB/s   00:00
id_rsa.pub                                   100%  400     0.4KB/s   00:00
2018211009@thumm02's password:
2018211009@thumm02's password:
authorized_keys                              100% 2001     2.0KB/s   00:00
id_rsa                                       100% 1675     1.6KB/s   00:00
id_rsa.pub                                   100%  400     0.4KB/s   00:00
2018211009@thumm03's password:
2018211009@thumm03's password:
authorized_keys                              100% 2001     2.0KB/s   00:00
id_rsa                                       100% 1675     1.6KB/s   00:00
id_rsa.pub                                   100%  400     0.4KB/s   00:00
2018211009@thumm04's password:
2018211009@thumm04's password:
authorized_keys                              100% 2001     2.0KB/s   00:00
id_rsa                                       100% 1679     1.6KB/s   00:00
id_rsa.pub                                   100%  400     0.4KB/s   00:00
2018211009@thumm05's password:
2018211009@thumm05's password:
authorized_keys                              100% 2001     2.0KB/s   00:00
id_rsa                                       100% 1679     1.6KB/s   00:00
id_rsa.pub                                   100%  400     0.4KB/s   00:00
```

之后就可以通过ssh thumm0**X**免密登录到**X**号节点了~

##### 7.2 集群批管理

集群中的thumm01上已经配置好了parallel-ssh集群管理工具，方便将文件复制到其他节点。
例如，我们需要查看thumm01-thumm05上的时间是否正确，我们需要在每台主机上运行date指令。使用pssh我们可以轻松实现在多主机上运行命令。

```
2018211009@thumm01:~$ pssh "date"
[1] 22:26:33 [SUCCESS] thumm04
[2] 22:26:33 [SUCCESS] thumm05
[3] 22:26:33 [SUCCESS] thumm01
[4] 22:26:33 [SUCCESS] thumm03
[5] 22:26:33 [SUCCESS] thumm02
```

parallel-ssh除了pssh外，还包括了pscp、pnuke、pslurp等指令， 使用这些指令我们控制集群中的节点会更轻松。

<div STYLE="page-break-after: always;"></div> 

##### 7.3 在多主机上并行执行任务

```sh
#!/bin/bash --login
pssh "mkdir -p ~/multi-nodes"             # 在thumm01-thumm05节点的主目录下创建multi-nodes目录
cd multi-nodes


lines=`cat ../wc_dataset.txt | wc -l`     # 计算wc_dataset.txt的行数
lines_per_node=$(($lines/5+1))              # 将wc_dataset.txt划分为5部分，计算每部的行数
split -l $lines_per_node ../wc_dataset.txt -d part  # 划分wc_dataset.txt为part00-part04

# 将不同的部分分别传至不同的节点
for ((i=0;i<5;i=i+1));do
    scp part0$i thumm0$(($i+1)):~/multi-nodes/part &
done
wait  # 等待节点传输完成

# 让每个节点运行任务，将结果保存在各自的~/multi-nodes/result文件中
pssh "grep '^t' ~/multi-nodes/part > ~/multi-nodes/result"

# 将所有节点的计算结果传至thumm01(当前操作的主机)
pslurp -L ~/multi-nodes/ ~/multi-nodes/result .

# 将所有结果整合成一个文件：t_head_multi_node.txt
rm -rf ~/multi-nodes/t_head_multi_node.txt
for ((i=1; i<=5; i=i+1)); do
    cat ~/multi-nodes/thumm0$i/result >> ~/multi-nodes/t_head_multi_node.txt
done

# 为了保证结果的正确性，接下来在单个节点上对wc_dataset.txt进行操作，并与多节点的结果作对比

# 在单节点上得到以t开头的单词
grep '^t' ~/wc_dataset.txt > ~/multi-nodes/t_head_single_node.txt

# 检查单节点和多节点的结果是否一致
diff ~/multi-nodes/t_head_multi_node.txt ~/multi-nodes/t_head_single_node.txt
```
运行上面的脚本，最后diff指令输出为空则表示单机与多机结果一致。

到这里我们就完成了多机实验了。


#### 任务8. 尝试提出一种可以加快多节点处理速度的方法（加分项）

<div STYLE="page-break-after: always;"></div> 

## 四、一些常见的问题

### 1. 找不到pssh指令

pssh的指令全称为parallel-ssh，可以通过ssh在多个指定主机上执行相同的命令。为了方便大家使用，这里通过宏定义隐藏了主机列表配置文件（/home/dsjxtjc/hostname）,为了使宏定义生效，当大家在脚本中使用pssh、pscp、pslurp等指令是需要在文件头添加

```sh
#!/bin/bash --login
```

然后给脚本添加运行权限

```sh
chmod +x ./test.sh
```

最后使用./test.sh的方式运行。（以bash ./test.sh方式运行会报错）


### 2. 关于作业提交

- 实验指导是大家做实验的一个参考，之后的作业也是和实验指导上的内容类似，只不过需要大家自己找一个数据集，然后照着实验指导上类似的做法（不需要一样，大家自由发挥~~）做，记录结果并写成实验报告提交的网络学堂的作业处。

- 有同学问到实验报告用截图还是直接用终端上的运行输出，回答是两种都行，大家只要自己在终端上操作一下，了解一些基本指令的用法就行~（实验指导里大部分指令是我觉得大家在之后科研中可能经常会用到的，多练练还是挺有用的~）

- 此次作业有两个加分项，一个是数据可视化，另一个是加快多节点处理的方法，方法有很多，第一个加分项不用做太复杂，第二个可以尝试多种方法，多多益善~~ 
