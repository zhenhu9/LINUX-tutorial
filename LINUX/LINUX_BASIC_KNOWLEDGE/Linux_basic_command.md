### Linux 简介

1991 年 Linus Torvalds 在芬兰的赫尔辛基大学开发了一个类 UNIX 的操作系统内核 LINUX。他不但是 Linux 内核的创建者，并且一直是 Linux 内核的主要开发者。他还创建了分布式版本控制系统 Git。

分发商基于 linux 内核又构建了许多 linux 系统发行版，最为常见为 Red Hat Enterprise Linux、CentOS、Ubuntu、Debian、Fedora、SUSE、Arch Linux、Oracle Linux、Clear Linux、Free BSD 和Open BSD 等。毋庸置疑 Red Hat Enterprise Linux 在商业化 Linux 系统中占主导地位，CentOS Linux 派生自 Red Hat，Inc. ，其为免费提供给公众的 Red Hat Enterprise Linux 资源。

学习提示！

对初学 Linux 系统的朋友来说，以 CentOS 或通过 Red Hat Developer 的免费订阅来作为学习 Linux 系统的切入口是一个很不错的选择。在熟练掌握 Linux 系统之前，最好还是先使用图形界面。另推荐 VMware WorkStation 桌面系列软件来构建多机学习环境。

------

#### Linux 文件系统架构简介

在登录界面输入用户名和密码后，疑问萦绕心中，我在哪里？我要去哪？我要干什么？

Linux 文件系统架构看起来像一个倒置的树，以根目录（`/`）起始，逐渐展开。

输入以下命令再按回车（`enter`）。将会输出 ascii 字符图形的目录结构。

```
# tree -L 1 /

/
├── bin -> usr/bin	/* 放置任何用户都可以执行的系统命令。
├── boot		/* 放置与系统启动相关的文件，如 Linux 系统的内核文件和启动引导文件 grub 等。
├── dev			/* 放置所有与系统连接的设备文件，如磁盘、硬盘、终端等。
├── etc			/* 放置主机的本地配置文件，如启动文件脚本、用户信息及服务配置文件等。
├── home		/* 普通用户家目录的默认放置目录。
├── lib -> usr/lib		/* 链接库文件目录。
├── lib64 -> usr/lib64		/* 64 位链接库文件目录。
├── lost+found		/* 用于保存系统发生崩溃时产生的文件碎片，便于修复文件系统。
├── media		/* 扩展存储设备挂载目录，如光驱、USB 等。
├── mnt			/* 设备或分区的临时挂载目录。
├── opt			/* 可选软件的安装目录。
├── proc		/* 虚拟目录，主要是系统内核、进程、扩展设备和网络等运行时的系统状态信息。
├── root		/* 超级管理员 root 的家目录。请注意与根目录 / 区分。
├── run			/* 虚拟目录，用于系统进程存放临时数据，如进程 ID 和套接字等。
├── sbin -> usr/sbin		/* 存放系统管理员使用的命令。
├── srv				/* 早期存放系统服务数据的目录。
├── sys			/* 虚拟目录，存放内核和设备信息的目录。
├── tmp				/* 临时文件目录，任何用户都可以使用。
├── usr			/* 全名 Unix Software Resource，是应用程序的默认安装目录。
└── var			/* 存放数据经常变化的目录，如缓存、日志等。
```

注： 如果显示命令未找到 `bash: tree: command not found...`，请先执行此命令`yum install -y tree` 来安装 `tree` 命令。

------

### Linux 基础命令

先熟悉一下环境，和各位命令先生打个照面。有些人很快就熟悉了，有些人要熟悉只是个时间问题！

------

#### 浏览文件系统及相关操作

首先，我们需要掌握如何浏览文件系统，如何在文件系统间移动，以及如何在文件系统中创建和删除目录。

------

**ls**

ls [*option* ...] [*directory* ...]

list directory contents.

显示目录内容。

```
-l			/* 长格式列表文件信息。
-d, --directory		/* 列表目录本身信息。

/* 列表所有文件，包括以 . 开头的隠藏文件。
-a, --all

/* 列表除了 `.`，`..` 这两个文件之外的所有文件。
-A, --almost-all

-h, --human-readable	/* 以 K、M、G 为单位列表文件大小。
-t			/* 列表时按修改时间排序文件。
```

示例：

```
/* 列表目录下的文件和目录。
# ls /home/example_user
Desktop Documents Downloads
Music Pictures Public
Templates Videos vmware

/* 以长格式列表当前所在目录内的文件信息，输入以下命令，
# ls -l /root
...
-rw-r--r--.  1 root root     9580 Sep  19 10:08 Hello-Lucky
...
```

以长格式显示的文件信息可分为九个信息区段：

```
第一个区段：第一个字符表示文件类型，如普通文件，目录等；

第二个区段：第二至第十位字符的九位字符称为文件权限模式位，表示文件的访问权限。其中每三个字符位分别表示为文件的属主、所属组和其他所有人三个类别划分集合的读、写、执行或无权限等。

第三个区段：也就是第十一个字符位，此样例中的那个点 . ，其所表示的含义为：

系统开启了 SElinux，文件未应用 ACL，此区段显示为 `.`；
系统关闭了 SElinux，之后创建的文件，也没未应用 ACL，此区段为空，之前开启 SElinux 创建的文件人仍然显示为 `.`；
无论系统是否开启 SElinux，只要文件应用了 ACL，此区段显示为 `+`；

第四个区段：文件的链接数或目录的第一级子目录数；

第五个区段：文件的属主；

第六个区段：文件的所属组；

第七个区段：文件大小（单位：字节）；

第八个区段：文件的修改时间；

第九个区段：文件名。
```

文件类型常见的有：

```
-		/* 普通文件。
d		/* 目录。
l		/* 链接文件。

c	/* 字符设备文件（用来表示串行端口的接口设备，例如伪终端等）。
b	/* 块设备文件（用来表示磁盘等）。
s	/* 本地域套接字文件（通常用在网络数据连接）。

p		/* 管道文件。
```

管道文件又分为具名管道和无名管道

具名管道: 一个特殊文件，其内容仅保存在内存中。

示例：

```
# useradd another_user		/* 在图形界面打开一个终端，并添加一个用户。
# mkfifo /tmp/named_pipe1	/* 创建具名管道文件。
或
# mknod /tmp/named_pipe1 p	/* 另一个创建具名管道的命令。

/* 从 root 终端向具名管道文件输入信息。
# echo "Hello World\!" > /tmp/named.pipe1

/* 打开另一个终端窗口，并登录到 another_user。
# su - another_user
/* 在 another_user 用户终端窗口查看具名管道文件中的信息。
$ cat < /tmp/named_pipe1

another_user 用户的终端将显示 Hello World! 信息。
```

无名管道:

无名管道不拥有管道文件。

```
/* 查看 /etc/passwd 文件，并通过无名管道 | 传送信息到命令 grep，以显示匹配 root 关键字的信息。
# cat /etc/passwd | grep "root"

将显示 `root:x:0:0:root:/root:/bin/bash` 信息。
```

------

**pwd**

print name of current/working directory.

输出当前/工作目录的路径。

示例：

```
# pwd		/* 显示当前工作目录的路径。
/root
```

------

**mkdir**

mkdir [*OPTION* ...] [*directory* ...]

make directories.

创建目录。

	-p		/* 按需创建父目录。

示例：

```
# mkdir test_dir	/* 创建目录 test_dir

/* 创建目录 child_dir 及其父目录 parent_dir。
# mkdir -p parent_dir/child_dir
```
------

**rmdir**

rmdir [*OPTION* ...] [*directory* ...]

remove empty directories.

移除空目录。

```
-p	/* 删除目录及其子目录，目录下不能含有其它另外创建的文件。
```
示例：

```
# mkdir -p a/b/c	/* 创建多级目录。
# rmdir -p a/b/c	/* mkidr -p 的反操作。
```

------

**cd**

cd [*directory*]

change the current directory to *directory*.

更改当前目录到 *directory*。

特殊字符

```
.		/* 表示当前目录。
..		/* 表示上一级目录。
~		/* 表示用户主目录。
-		/* 表示之前所在的目录。
```

示例：

```
# cd /boot	/* 进入根目录下的 boot 目录。
# pwd		/* 显示当前目录路径。
/boot

# cd      	/* 不加参数，表示返回用户家目录，等同 `cd ~`。
# pwd		/* 显示当前目录路径。
/root

# cd -    	/* 返回之前所在的目录。
# pwd
/boot
```

绝对路径与相对路径：绝对路径是以根目录 `/` 为起始的路径；相对路径是相对与当前路径或特定路径的路径。

示例：

```
# cd /var/log		/* 以绝对路径的方式进入目录。

# cd			/* 回到家目录。
# cd ../var/log		/* 以相对路径的方式进入目录。
```

------

#### 查看文件的类型、状态及修改

**file**

determine file type
检测文件的类型。

示例：

```
# file ~/.bashrc
/home/haojiang/.bashrc: ASCII text	/* 显示其为 ASCII 文本文件。

# file /dev/tty
/dev/tty: character special (5/0)	/* 显示其为特殊字符文件。
```

------

**stat**

display file or file system status
显示文件或文件系统的状态。

示例：

```
# stat ~/.bash_profile

  File: /home/haojiang/.bash_profile
  Size: 141       	Blocks: 8          IO Block: 4096   regular file
Device: fd02h/64770d	Inode: 268762396   Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1001/haojiang)   Gid: ( 1001/haojiang)
Context: unconfined_u:object_r:user_home_t:s0
Access: 2021-01-04 08:04:27.191348732 -0500
Modify: 2020-07-21 11:59:41.000000000 -0400
Change: 2021-01-04 08:02:45.921860542 -0500
 Birth: -

/* 信息量比较多，先认识一下就好。
```

------

**touch**

touch [*OPTION* ...] [*file* ...]

change file timestamps or create *file*.

更改文件时间戳或创建文件。

```
-a		/* 仅改变访问时间。
-m		/* 仅改变修改时间。
```

每个文件都具有三个时间： 访问时间（access time）、修改时间（modify time）、属性变化时间（change time）。当文件被首次创建时间时，三个时间相同；当用 cat、less、more 查看时，访问时间被记录；当改变文件属性，如权限等时，属性变化时间被记录；当修改文件内容时，修改时间和属性变化时间都同时被记录。

示例：

```
# touch stamp_test.txt	/* 如果指定文件不存在，则创建。
# stat stamp_test.txt	/* stat 查看文件状态，其中包括三个相同时间记录。
...
Access: 2020-11-12 17:26:25.592183057 +0800
Modify: 2020-11-12 17:26:25.592183057 +0800
Change: 2020-11-12 17:26:25.592183057 +0800

# cat stamp_test.txt
# stat stamp_test.txt	/* 执行 cat 命令后，访问时间放生变化。
...
Access: 2020-11-12 17:33:21.041257391 +0800
Modify: 2020-11-12 17:26:25.592183057 +0800
Change: 2020-11-12 17:26:25.592183057 +0800

# chmod o+w stamp_test.txt	/* chmod 改变文件权限。
# stat stamp_test.txt	/* 执行 chmod 命令后，属性变化时间发生变化。
...
Access: 2020-11-12 17:33:21.041257391 +0800
Modify: 2020-11-12 17:26:25.592183057 +0800
Change: 2020-11-12 17:37:42.467079091 +0800

# echo "hello" >> stamp_test.txt	/* 修改文件内容。
# stat stamp_test.txt	/* 修改时间和属性变化时间发生变化。
...
Access: 2020-11-12 17:33:21.041257391 +0800
Modify: 2020-11-12 17:39:44.790867299 +0800
Change: 2020-11-12 17:39:44.790867299 +0800
```

------

#### 输出指定信息和查看文件内容

**echo**

echo [*OPTION* ...] *TEXT*...

display a line of text.

输出一行文本。

```
-n		/* 取消输出默认在行尾输出的换行符。
-e		/* 开启转译字符解析。
-E		/* 关闭转译字符解析。
```

如果 `-e` 选项生效，则可以识别以下转译序列：

```
\\		反斜线
\a		响铃符(BEL)
\b		退格符

不再进一步的输出（解析此字符后，则其后信息不再输出，也没有换行）
\c
\f		换页符
\r		回车符
\n		换行符
\t		水平制表符
\v		垂直制表符

\NNN	字符的 ASCII 代码为 NNN (八进制)
\xHH	字符的 ASCII 代码为 NNN (十六进制)
```

注：单引号中的字符串按原样输出，含有需要转译的字符串一般用双引号括起。

示例：

```
# echo 'Hello World!'	/* 输出指定字符文本。
Hello World!

/* 乐一下
# echo -e "\033[?25l"

/* 有惊喜
# echo -e "\033[?25h"
```

小技巧：在中英文输入切换的时候，有时会因为汉字输入中断而丢失光标，要么切换回中文输入，要么用以上方法找回光标。

------

**cat**

cat [*option* ...] [*FILE*...]

concatenate files and print on the standard output.

连接文件并输出到屏幕。

```
/* 缺省文件名参数或文件名为连字符 - 时，则读取标准输入。

/* 输出时显示不可打印字符，包括制表符和换行符。等同 -vET。
-A, --show-all

/* 用 ^ 和 M- 显示不可打印字符，但不包括 LFD 和 TAB。
-v, --show-nonprinting
-e			/* 输出不可打印字符和换行符。等同 `-vE`。
-E, --show-ends		/* 在行尾以 $ 符号显示换行符。
-t			/* 等同 -vT。
-T, --show-tabs		/* 输出时以 `^I` 显示制表符。

-n, --number		/* 显示时列行号。
-b, --number-nonblank	/* 仅对非空行列行号。覆盖 -n。
-s, --squeeze-blank	/* 压缩重复的空行。

--help			/* 显示帮助。
--version		/* 显示命令版本。
```

`cat` 一般用于查看内容比较少的文本文件，或配合重定向符号创建文本文件（Shell 编程中使用较多）。查看非打印字符是否是你所需要的，比如制表符和行尾的换行符是否符合 Linux 标准的 `$`。

示例：

```
/* 在当前目录生成 test1.txt 文本文件。
# cat > test1.txt << EOF
abc
bbc
ccc
EOF

注：> 与 << 为重定向符号，EOF 表示输入结束（END OF FILE）。

# cat -A test1.txt	/* 查看文本文件内容，并显示不可打印字符。
abc$
bbc$
ccc$

# unix2dos test1.txt	/* 将文本文件从 unix 格式转换为 dos 格式。

# cat -e test1.txt	/* 查看 dos 格式文本文件的换行符号。
abc^M$
bbc^M$
ccc^M$

# dos2unix test1.txt	/* 将 dos 格式的文本文件转换为 unix 格式。
# unix2mac test1.txt	/* 将 unix 格式的文本文件转换为 mac 格式。
# mac2unix test1.txt	/* 将 mac 格式的文本文件转换为 unix 格式。
```

注：Windows 系统中文本文件的换行标记为回车符换行符（CR+LF），Unix 为（LF），Mac 为 CR。

------

**less** / **more**  [*FILE* ...]

两个都是分页察看器，用于查看大文件，分屏显示，但 more 只能向后查看。

```
space		/* 显示下一屏内容。
b		/* 显示上一屏内容。
f		/* 显示下一屏内容。

/keywork	/* 查找关键字 keyword。
	n	/* 下一个 keyword。
	N	/* 上一个 keyword。

q		/* 退出分页查看器。
```

注：一般用 less 就可以了。

------

**head**

head [*OPTION* ...] [*FILE* ...]

output the first part of files.

输出文件前部分，默认 10 行。

示例：

	# head -5 /etc/passwd		/* 输出指定文件前 5 行信息。

------

**tail**

tail [*OPTION* ...] [*FILE* ...]

output the last part of files.

输出文件的末尾部分，默认 10 行。

示例：

	# tail -5 /etc/passwd		/* 输出指定文件最后 5 行信息。

------

**grep**

grep [*OPTIONS*] *PATTERN* [*FILE*...]

print lines matching a pattern.

输出文件内匹配指定模式的行。

```
-i		/* 匹配时，忽略大小写。
-v		/* 显示不匹配的行。
-n		/* 在每个匹配行前面加上行号。
-c		/* 仅打印匹配行的行数。

注：这是一个非常强大的查询命令，关联非常强大的正则模式，此处先简单介绍几个选项。
```

示例：

```
# grep root /etc/passwd		/* 输出匹配关键字 root 的行。

# cat > test2.txt << EOF	/* 创建一个文件。
abc
ABc
EOF

/* 输出匹配关键字 abc 的行，并且忽略大小写。
# grep -i 'abc' test2.txt
abc
ABc

# grep -v abc test2.txt		/* 输出不匹配的行。
ABc

# grep -ic abc test2.txt	/* 输出匹配的行数。
2
```

------

**wc**

wc [*OPTION* ...] [*FILE* ...]

print newline, word, and byte counts for each file.

输出每个文件的行数，词数或所用字节数。

```
-c, --bytes		/* 输出文件的字节数。
-m, --chars		/* 输出文件的字符数。
-l, --lines		/* 输出文件的行数。
-w, --words		/* 输出文件单词数。
-L, --max-line-length	/* 输出文件最长行的行长。
```

示例：

```
# wc -l /etc/passwd	/* 查看文件有多少行。
52 /etc/passwd

# wc -L /etc/passwd	/* 查看最长行长。
99 /etc/passwd
```

------

#### 文件及目录的复制、改名、移动和删除

**cp**

cp [*OPTION* ...] *SOURCE* *DEST*

copy files and directories.

复制文件和目录。

```
/* 递归操作，将指定目录下的所有文件与子目录递归复制。
-R/r, --recursive

/* 此参数的效果等同 -dR --preserve=all。递归复制，并维持源文件所有属性。
-a, --archive

/* 使用此项参数后，只会在源文件的更改时间比目标文件要新的时候，或是目标文件并不存在时，才复制文件。
-u, --update

-d, 		/* 复制符号链接时，不会破坏链接文件和源文件链接关系。
-l, --link	/* 对源文件建立物理链接，而非复制文件。
-s		/* 对源文件建立符号连接，而非复制文件(指定绝对路径)。

-i, --interactive		/* 覆盖既有文件之前先询问用户。
-p		/* 保留源文件或目录的权限模式位、属主和时间戳属性。

-b		/* 复制文件，如有同名文件，则在文件名后添加后缀 ~。

-v, --verbose			/* 详细显示命令执行的操作。
```

示例：

指定和不指定目标文件名的却别。
```
# cp test1.txt /tmp/test1.txt.backup
# cp test1.txt /tmp			
# ls -l /tmp/test1.*		/* 查看两次复制结果。
```

交互复制。
```
# cp -i test1.txt /tmp		/* 交互模式复制，提示是否覆盖。
```

递归复制。
```
# mkdir -p  a/b/c				
# cp -R a /tmp			/* 递归复制 a 目录及其下目录和文件。
# ls -l /tmp/a/b		/* 查看递归复制的结果。
```

备份复制。
```
/* 不覆盖同名文件，生成新文件 /tmp/test1.txt~，再次复制则覆盖。
# cp -b test1.txt /tmp			
# ls -l /tmp/test1.*
```

更新复制。
```
# cp test1.txt /tmp/
/* 添加文件内容。
# echo "add something into sour_file" >> test1.txt
# cp -u test1.txt /tmp/		/* 更新目标文件。

# cat /tmp/test1.txt	
# echo "add something into dest_file" >> /tmp/test1.txt
# cp -u test1.txt /tmp/		/* 目标文件比源文件新，则不复制。
```

注： 一般 -a 选项就能很好的满足日常使用，递归复制并且完全保留了源文件的属性。

------

**rm**

rm [*OPTION* ...] [*FILE* ...]

remove files or directories.

移除文件或目录。

```
-r, R, --recursive	/* 递归删除。
-i			/* 交互式删除，删除前提示。
-f, --force		/* 强制删除，删除前不提示。
```

示例：

```
# touch one.txt		/* 创建一个空文件。
# rm	one.txt		/* 删除一个文件。

# mkdir -p a/b/c
# cp test1.txt a/b/c
# rm -R a		/* 删除 a 目录及其下所有目录和文件。
```

注：`rm -r/Rf` 应该是 Linux 系统中最危险的操作，没有之一。在生产环境中，执行此命令前，请仔细查看命令。做好备份才是万全之策。

------

**mv**

mv [*OPTION* ...] *SOURCE* ... *DIRECTORY*

move (rename) files.

移动或改名文件。

```
-i, --interactive	/* 覆盖前给出提示。
```

示例：

```
# touch regular_file		/* 创建一个空文件。
# mv regular_file desired_file	/* 更改文件名。

# mkdir a
/* 把 a 目录改名为 b 目录，如果 b 目录存在，则会移动到 b 目录下。
# mv a b
```

------

#### 重定向、无名管道与通配符

/* 重定向符号

```
>		/* 将数据流导入到指定文件、命令或设备。
>>		/* 以追加的方式导入到指定文件、命令或设备。
<		/* 将数据流导入到指定文件、命令或设备。
<<		/* 以追加的方式导入到指定文件、命令或设备。

注：箭头方向指定目标。
```

/* 无名管道符号

```
|		/* 将一个命令输出的数据导入到另一个命令。
```

/* 通配符号

```
*		/* 在命令参数中用来表示 0 或 n 个任意字符。
?		/* 在命令参数中用来表示 1 个任意字符。
```

示例：

```
/* 将字符串输出导入到指定文件。指定文件若不存在则创建，否则覆盖其内容。
# echo 'hello world' > test3.txt

# echo 'bye' >> test3.txt	/* 将字符串以附加方式输入到指定文件。

# touch abc bbc bc	/* 创建三个空文件。

# ls ?bc
abc bbc
# ls *bc
abc bbc bc		/* 比较两次列表文件的结果。
```

此处文档（Here Document）

通常用于将输入导入到交互 SHELL 脚本或程序。

```
command << delimiter	/* delimiter 为指定的输入符，通常指定为 EOF。
document		/* 此处为输入。
delimiter		/* 结束符表示输入结束。
```

示例：

```
/* 将键盘输入的信息导入到 cat 命令，cat 命令再输出到指定文件，指定输入结束符为 EOF。
# cat > test2.txt << EOF
abc
ABc
EOF

注：如果未指定输入结束符，按 ctrl + d 也可以结束输入。
```

------

#### 符号链接和物理链接

符号链接也称为软链接，是一个包含文件或目录名称的小文件。
物理链接也称为硬链接，是一个将文件名与文件相关联的目录条目，也可以说是文件的别名。

------

**ln**

ln [*option* ...] *TARGET* *LINK_NAME*

ln [OPTION]... TARGET... DIRECTORY

make links between files.

创建文件间的链接。

```
第一种格式：对指定的文件 TARGET 创建指定链接名 LINK_NAME。
第二种格式：对指定的文件 TARGET 在指定的目录 DIRECTORY 创建同名链接。

-P, --physical		/* 创建硬链接。
-s, --symbolic		/* 创建软链接。

注：文件路径尽量使用绝对路径，否则会创建无效链接。
```

示例：

```
# touch regular_file

# ln -P regular_file hardl_file		/* 创建硬链接。
# ln -s regular_file softl_file		/* 创建软链接。
```

------

#### 文件压缩、打包和归档命令

**gzip**

gzip [*OPTION*] [*FILE* ...]

compress or expand files.

压缩或解压缩文件。

```
/* 输出压缩结果，保留原文件，常配合重定向使用。
-c, --stdout --to-stdout
-d, --decompress, --uncompress		/* 解压缩。
-r, --recursive		/* 递归压缩目录内文件，只压缩文件，不压缩目录。
-1~9	/* 指定压缩级别，-1 最快压缩，-9 最大压缩，更消耗 cpu ，默认为 -6。
```

示例：

```
# cp /usr/bin/zipinfo ./gz_test		/* 复制文件。
/* 压缩指定文件，生成的压缩文件自动添加扩展名 .gz，源文件被删除。
# gzip gz_test

# gzip -d gz_test.gz	/* 解压缩文件，.gz 文件被删除。

/* 压缩数据被重定向到指定文件，源文件保留。
# gzip -c gz_test >> gz_test.gz

# cp -r /etc/yum.repos.d/ ./gz_dir	/* 复制目录。

# gzip -r ./gz_dir	/* 递归压缩目录内文件。
# ls -l ./gz_dir

# gzip -rd ./gz_dir	/* 递归解压缩目录内文件。
# ls -l ./gz_dir
```

------

**bzip2**

bzip2 [*OPTION* ...] [*FILE* ...]

压缩或解压缩文件。

```
-k, --keep		/* 保留源文件。默认选项。
-d, --decompress	/* 解压缩文件。
-1~9			/* 指定压缩级别。
```

示例：

```
# cp /usr/bin/zipinfo ./bz2_test
/* 压缩文件,保留源文件并生成文件 bz2_test.bz2。
# bzip2 -k bz2_test
# bzip2 -d bz2_test.bz2		/* 解压缩。
```

------

**zip**

打包并且压缩文件。

```
-r, --recurse-paths	/* 递归压缩目录。
-m, --move		/* 删除源文件。默认不删除。
```

示例：

```
# cp -r /etc/yum.repos.d/ ./zip_dir	/* 解压开始用 gzip 递归压缩目录内文件。
# zip -rm zip_dir.zip test_dir		/* 递归压缩目录和文件，并删除源文件。
```

------

**unzip**

解压 zip 压缩的文件。

示例:

	# unzip test_dir.zip		/* 解包和压缩。

------

**tar**

tar [*OPTION* ...] [*arg* ...]

an archiving utility.

归档工具

/* 操作选项

```
-c, --create		/* 创建新的 tar 包。
-t, --list		/* 查看 tar 包中的目录和文件。

/* 比较归档与文件系统的区别，默认为当前目录，要么必须显示地指定要比较的文件或目录。
-d, --diff, --compare
--delete		/* 删除归档中指定的文件，但不能对压缩的文档操作。

/* 追加归档到另一个归档后面，但追加多个归档时，要指定 -i。无法对已压缩归档操作。
-A, --catenate, --concatenate
/* 结合 -f 选项，添加文件到归档文件。也可创建新归档。
-r, --append

/* 当文件比归档中的文件新时，就追加到归档，但不会替换旧的文件。也可创建新归档。
-u, --update

-x, --extract, --get		/* 从归档文件中析取文件。
```

/* 指定归档文件名

```
-f, --file=ARCHIVE	/* 指定 tar 包名。
```

/* 链接文件选项

```
-h, --dereference	/* 归档时导入符号链接所指向的文件。
--hard-dereference	/* 归档时导入硬链接所指向的文件。
```

/* 匹配

```
--exclude=PATTERN	/* 排除匹配的文件，glob(3) 通配符匹配。
-X, --exclude-from=FILE		/* 从指定的文件中读取排除的匹配。
/* 从指定的文件中读取文件以析取或创建归档，每行一个文件。
-T, --files-from=FILE
```

/* 对文件属性的操作选项

```
-i, --ignore-zeros	/* 忽略归档中的零块，对读取 -A 创建的归档很有用。
/* 保留文件权限位，默认超级用户。
-p, --preserve-permissions, --same-permissions
-s, --preserve-order, --same-order	/* 排序归档中的文件名。
--preserve		/* 等同 -p 和 -s。
```

/* 压缩选项

```
-a, --auto-compress	/* 根据归档文件名后缀自动指定压缩程序。
-I, --use-compress-program=COMMAND	/* 指定压缩程序。

-z, --gzip, --gunzip, --ungzip		/* gzip 格式压缩， 后缀为 .tar.gz。
-Z, --compress, --uncompress		/* compress 格式压缩，后缀为 .Z。
-j, --bzip2		/* bzip2 格式压缩，后缀为 .tar.bz2。
-J, --xz		/* xz 格式压缩，后缀为 .tar.xz。
--lzip			/* lzip 格式压缩，后缀为 .tar.lz。
--lzma			/* lzma 格式压缩，后缀为 .tar.lzma。
--lzop			/* lzma 格式压缩，后缀为 .tar.lzo。
```

/* 其它选项

```
--one-file-system	/* 创建归档时，归档的文件局限在本地文件系统。
-P, --absolute-names	/* 保留文件名路径前置斜杠。
-C, --directory=DIR	/* 析取文件到指定目录。
--remove-files		/* 归档后删除源文件。
-v, --verbose		/* 繁琐模式显示执行过程。
-?, --help		/* 输出选项的简短概览。
```

提示：对初学 Linux 的朋友，如此多的选项，想必很让人抓狂，但这还只是此软件功能中的一部分。无论图形界面还是命令行，我们会意识到他们的通性，某类软件一般只干某一类的事，他们都会有相似的功能；其次，软件功能分类在图形菜单中和命令行中也是类似的；另外，大部分功能是给编程人员使用的，或在特定使用中经过查询、测试后才使用的。

在学习的过程中，我们会查询大量的英文手册页，能够快速的浏览英文手册页是一个必定的过程。虽然如今 Linux 系统版本内建手册页的命令解释文档比早期的好多了，但对初学者来说，最好还是到手册页给出的官网网站去找新手指导类的文档或书籍，或者直接搜索引擎查找相关资料。

常用选项示例：

```
# tar -czvf system.tar.gz /etc		/* 归档 /etc 目录并使用 gzip 压缩。
# tar -cavf system.tar.xz /etc		/* 以归档文件扩展名自动判断压缩格式。

# tar --list -f system.tar.gz		/* 查看归档内容。
/* 查询归档中是否有匹配 ifcfg 的网络接口文件。
# tar --list -f system.tar.gz | grep ifcfg

# tar xzvf system.tar.gz		/* 析取归档中的文件到当前目录。
# tar xzvf system.tar.gz -C ./tar_test	/* 析取归档中的文件到目录 ./tar_test。

/* 打包时排除 yum.repos.d 软件仓库源配置目录。
# tar cjvf system.tar.bj2 --exclude=/etc/yum.repos.d /etc
/* 查询目录是否被排除。
# tar --diff -vf system.tar.bj2 yum.repos.d
tar: yum.repos.d: Not found in archive

/* 加个日期以便日常备份的识别，方便记忆哪天没有做 ymd 备份。
# tar czvf system_etc_`date +%y_%m_%d`.tar.gz /etc
```

------

#### 帮助类命令

出发新手村，我们除了会在官网查询所需相关信息，还会找到系统 NPC 来查询所需信息。下面将介绍几个系统 NPC。另外要记住命令的通用选项 --help 和 --version，分别用来请求帮助和了解命令的版本信息。

**which**

shows the full path of (shell) commands.

输出（shell）命令的完整路径。

示例：

```
# which gzip
/usr/bin/gzip

# which cd
/usr/bin/cd
```

注：which 无法区分 bash 内置命令和外部命令，查询 bash 内置命令请用 enable。

------

**whereis**

locate the binary, source, and manual page files for a command.

查找命令的二进制文件、源码及其手册页文件位置。

示例：

```
# whereis less
less: /usr/bin/less /usr/share/man/man1/less.1.gz /usr/share/man/man3/less.3pm.gz
# whereis rm
rm: /usr/bin/rm /usr/share/man/man1/rm.1.gz /usr/share/man/man1p/rm.1p.gz
```

------

**whatis**

display one-line manual page descriptions.

查询并输出所有匹配指定关键字的命令，并且输出其手册页中的单行描述。

示例：

```
# whatis cd
cd (1)               - bash built-in commands, see bash(1)
cd (1p)              - change the working directory

# whatis cp
cp (1)               - copy files and directories
cp (1p)              - copy files

# whatis whatis
whatis (1)           - display one-line manual page descriptions
```

注：whatis 分别显示了 bash 内置命令和外部命令（或称系统命令）。

------

**apropos**

search the manual page names and descriptions.

通过匹配手册页中的命令名和描述来查找命令。

示例：

```
/* 通常用于查询不熟悉命令，只要输入命令名可能的部分字符或其功能来查找命令。
/* 比如此页面中的三个压缩命令。
/* 在查询的结果中寻找可能是你所需的命令。

# apropos zip
...
bzip2 (1)            - a block-sorting file compressor, v1.0.6
...
funzip (1)           - filter for extracting from a ZIP archive in a pipe
gunzip (1)           - compress or expand files
gzip (1)             - compress or expand files
...
zip (1)              - package and compress (archive) files
...

# apropos compress		/* 以命令的功能来查询所需的命令。
...
bzip2 (1)            - a block-sorting file compressor, v1.0.6
compress (1)         - compress and expand data
compress (1p)        - compress data
gunzip (1)           - compress or expand files
gzip (1)             - compress or expand files
uncompress (1)       - compress and expand data
uncompress (1p)      - expand compressed data
unxz (1)             - Compress or decompress .xz and .lzma files
unzip (1)            - list, test and extract compressed files in a ZIP archive
xz (1)               - Compress or decompress .xz and .lzma files
xzcmp (1)            - compare compressed files
zip (1)              - package and compress (archive) files
...
```

-----

**man**

an interface to the on-line reference manuals.

在线参考手册页接口。

whatis，whereis，apropos 都是 man 命令的部分实现，只是更容易记忆。

手册页分为 9 大类，请用命令 man man 查询以下类似信息：

```
1   Executable programs or shell commands			# 可执行程序或 shell 命令。
2   System calls (functions provided by the kernel)		# 系统调用。
3   Library calls (functions within program libraries)		# 链接库调用。
4   Special files (usually found in /dev)			# 特殊文件。
5   File formats and conventions eg /etc/passwd			# 文件格式和约定。
6   Games							# 游戏。
7   Miscellaneous  (including  macro  packages  and  conventions), e.g.
    man(7), groff(7)						# 杂项。
8   System administration commands (usually only for root)	# 系统管理员命令。
9   Kernel routines [Non standard]				# 内核线程。
```

man [1-9] command

示例：
	man 1 more 

------

####  其它命令

**alias**

创建命令别名。
	
	-p		/* 以 `name=value` 键值对的格式输出已定义的命令别名。

alias 不加参数将显示所有已设置的别名。

注：在命令行定义的别名要在其它会话中生效，必须将整个别名定义添加到 shell 系统配置文件 `/etc/bashrc` 或 shell 个人配置文件 `.bashrc` 文件中。

------

**unalias**

remove each name from the list of defined alias.

移除指定的别名。


	-a			# 移除所有已经定义的别名。

同样删除别名要在其它会话中生效，必须修改 shell 系统配置文件 `/etc/bashrc` 或 shell 个人配置文件 `.bashrc` 文件。

------

**date**

date [*OPTION* ...] [+FORMAT]

print or set the system date and time.

输出或设置系统日期和时间。

```
-I[FMT], --iso-8601[=FMT]	/* 输出 ISO 8601 时间格式。默认 FMT='date'。
--rfc-3339=FMT		/* 输出 RFC 3339 时间格式。默认 FMT='date'。
			/* FMT 可以为 date、hours、minutes、seconds 或 ns。

-R, --rfc-email		/* 输出 RFC 5322 时间格式。
-u, --utc, --universal	/* 输出或设置 Coordinated Universal Time (UTC) 时间。

-d, --date=STRING	/* 输出 STRING 指定的时间。
-s, --set=STRING	/* 设置 STRING 指定的时间。
/* STRING 可以为：
yesterday, today, tomorrow, next-day, next-month, next-year, last-day, last-month, last-year, 或'next-day'；
 
2020-01-01；

或格式化控制输出。可解析时间序列很多。
```

下面将示例几个例子，详细的可解析序列，请查看手册页。查看手册页是个好习惯。

```
/* 输出纪元时加所指定的秒数的时间，纪元时为 1970-01-01。
# date --date='@123456789'
Fri Nov 30 05:33:09 CST 1973
# date --date='2020-01-01'
Wed Jan  1 00:00:00 CST 2020

# date +%c
Thu 07 Jan 2021 05:31:47 PM CST

# date '+%F %T'
2021-01-07 17:32:53

/* 创建文件名具有日期的归档。反引号括住的代码先执行。
# tar -czvf system_etc_`date +%y_%m_%d`.tar.gz
```

------

**reboot**

重启电脑

------

**shutdown**

shutdown [OPTION ...] [TIME] [WALL...]

关机

```
-H, --halt		/* 停止计算机。等同 --poweroof
-P, --poweroff		/* 关机。
-r, --reboot		/* 重启。
-h			/* 等同 --poweroff。

/* 指定时间。
now == +0		/* 现在。
+1 == +1m		/* 一分钟后。
hh:mm			/* 指定具体时间。	
```

示例：

```
# shutdown -r now		/* 现在立刻重启电脑。
# shutdown -h now		/* 立刻关机。
# shutdown -h +10 "I will shutdown the service in 10 minutes!"
/* 10 分钟后关机，并广播提示关机信息。
```

-----
