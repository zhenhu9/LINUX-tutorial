
------

## 用户管理和访问控制

------

### 文件系统访问控制

“In Linux everything is a file.” 也就是说 Linux 中所有的东西都以文件实现，比如文本文件、目录、磁盘、进程等。

一般来说，个人拥有自己创建的资源，同组的人拥有共同分享的资源，当然还有个其他人。这时就引入了文件所有权的概念：文件的属主（user）、所属组（group）和其他人（other）。

对文件的操作一般会是，打开、读取、编写、执行、修改、删除、复制或者无法操作等。这又有了文件的访问权限，读（read）、写（write）、执行（execute）和无权限（no permission）。那么文件的属主、所属组和其他人就分别具有了对文件的各自访问权限。

注: 超级用户 root 管理系统中的所有资源。权力越大，责任越大。

所有权表

| 所有权 | 属主    | 所属组   | 其他人     |
| ------ | ------- | -------- | ---------- |
| ownership   | user    | group      | other          |
| 符号表示法  | u       | g          | o              |

访问权限表

|访问权限    | access permission | 符号表示法 | 八进制位表示法 |
| --------   | ------- | ---------- | -------------- |
| 读         | read    | r          | 4              |
| 写         | write   | w          | 2              |
| 执行       | execute | x          | 1              |
| 无权限     | no permission | -          | 0              |
| 全权限     | full permission | rwxrwxxrwx | 777          |

```
ls -l		# 长格式列表当前目录内容。
...
-rw-r--r--.  1 root root     9580 Sep  19 10:18 Hello-Lucky
...
```

新建一个文件，其所有权的属主和所属组通常分别为创建者和与创建者账户同名的组。文件的访问权限（也可以称为，文件的访问模式），读、写和执行权限可以反映文件的属主、所属组和其他人可以如何访问文件。

新建文件的默认访问权限由 `umask` 的值决定，可以在命令行执行 `umask` 以输出其值。超级用户 root 的默认所属组为 root，正如 Hello-Lucky 文件信息所示。

新建目录的默认访问权限为全权限（rwxrwxrwx 或 777 ，也就是 4+2+1=7，4+2+1=7，4+2+1=7）按位减去模式位掩码值（umask 值）。

新建文件的默认访问权限为不可执行文件的全权限（rw-rw-rw- 或 666）按位减去模式位掩码值。


新建目录与文件的访问权限表

| 用户账号分类 | 文件的类型 | 全权限     | umask 值 | 实际权限 |
| ----       | ---------- | ---------  | -------- | -------- |
| root       | 目录       | 777        | 022      | 755      |
| root       | 文件       | 666        | 022      | 644      |
| 普通用户   | 目录       | 777        | 002      | 775      |
| 普通用户   | 文件       | 666        | 002      | 664      |

------

#### 读、写和执行权限

文件的访问权限：

```
读-允许打开并读取文件内容。

写-允许修改文件内容。

执行-允许文件被执行（如果文件是可执行文件）。
```

目录的访问权限：

读和执行权限共同控制着目录内容的列出；写和执行权限共同控制着在目录中创建、删除或重命名文件。

注： 文件名和数据存储空间之间的映射实际上是保存在目录中的。

```
读-控制着是否可以列表目录内文件的名称及类型。如果同时具有执行权限，则可以打开和读取文件的内容及查看文件的其它相关信息。

写-控制着是否可以在此目录下创建、删除和重命名文件，但前提是必须同时具有执行权限。

执行-控制着是否可以进入目录并打开和读取文件的内容，并且还可以查看文件名和类型之外的其它相关信息。
```

注：在用户具有写和执行权限的目录下，只读文件的内容能够被删除和修改（例如用 vi、vim 等强行修改）。可以强行修改的原因是目录赋予删除的权限（用户具有目录写和执行权限），实际修改的过程是删除后再创建了同名文件，并且文件的所有权变成了修改的用户。

------

#### 用户管理

与用户账号信息相关联的文件:

```
/etc/passwd			/* 用户账号信息。
/etc/shadow			/* 用户账号安全信息。
/etc/group			/* 组信息。
/etc/gshadow			/* 组安全信息。
/etc/default/useradd		/* 账号创建默认值。
/etc/login.defs			/* 账号创建配置套件。
```

```
# cat /etc/passwd

root:x:0:0:root:/root:/bin/bash
...
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin
...
```

/etc/passwd 文件中每行记录一个用户，包含由冒号分隔的 7 个字段。字段定义如下：

```
用户名
加密密码的预留位置
UID（User Identification Number，用户 ID）数字
默认的 GID（Group Identification Number，组 ID）数字
GECOS（General Electric Comprehensive Operating System）字段（一般称为注释字段），此字段一般为空
用户家目录路径
登录 shell
```

```
# cat /etc/shadow

root:$6$m1WStj.gXGRhKsr.$SZAFjyEz4R.Tfg66htL0FFUNYkrN.dgr3Pr.L/SVA3MQkQypQ21.VyXbX3cLz9bZ8.qr8x.::0:99999:7:::
```

/etc/shadow 文件对应于 /etc/passwd 文件，每行记录一个用户，包含由冒号分隔的 9 个字段。

```
用户名
加密后的密码
上一次更改密码的日期
密码更改的最小间隔天数
密码更改的最大间隔天数
提前警告用户密码即将过期的天数
密码过期后多少天禁用该账户
账户过期日期
备用的保留字段，目前还空着
```

------

#### 用户管理相关命令：

id、useradd、passwd、userdel、usermod、groupadd、groups、groupdel、chown

------

**id**

id *USER*

输出用户 ID 和 组 ID。

示例：

```
# id			/* 输出当前用户的 ID 信息。
uid=0(root) gid=0(root) groups=0(root)

# id root		/* 输出指定用户的 ID 信息。
```

------

**useradd**

useradd [*OPTION* ...] *LOGIN*

create a new user or update default new user information.

创建一个新用户或更新新用户默认信息。

```
-r, --system			/* 创建系统用户。

/* 以下选项修改新用户默认信息。
-s, --shell SHELL		/* 指定 shell，默认 bash shell。
-g, --gid GROUP			/* 指定所属组（必须是已存在的组）。
-M, --no-create-home		/* 不创建用户家目录。
```

示例：

```
# useradd -r system_user009	/* 添加一个系统用户。
# id system_user009		/* 查看用户 UID 和 GID。
uid=997(system_user009) gid=995(system_user009) 组=995(system_user009)

# grep system_user009 /etc/passwd		/* 查看 /etc/passwd 文件。
system_user009:x:997:995:system_user009:/home/system_user009:/bin/bash

# grep system_user009 /etc/shadow		/* 查看 /etc/shadow 文件。
...

/* 创建一个没有家目录，并且 shell 为 /sbin/nologin 的用户。
# useradd -M -s /sbin/nologin nohome_nologin_user009
# grep nohome_nologin_user009 /etc/passwd	/* 查看用户信息。
nohome_nologin_user009:x:1002:1002::/home/nohome_nologin_user009:/sbin/nologin
# ls -l /home			/* 查看用户目录是否如指定的未创建。

# groupadd fun_club				/* 添加一个组。
# useradd -g fun_club fun_member01		/* 添加用户并指定组。
# id fun_member01				/* 查看用户 UID GID。
uid=1003(fun_member01) gid=1007(fun_club) groups=1007(fun_club)
```

------

**userdel**

userdel [*OPTION* ...] *LOGIN*

delete a user account and related files.

删除一个用户账户及其相关文件。

```
-r, --remove			/* 删除用户账号，并且删除其家目录和邮箱目录。
-f, --force			/* 强制删除用户，即使用户还在登录状态。
```

示例：

```
# useradd test_user001		/* 添加一个用户。
# id test_user001		/* 查看用户 ID 以检查用户是否添加成功。
# userdel test_user001		/* 删除用户，默认不删除其家目录和邮箱目录。
# ls -al /home			/* 查看 /home 中的用户家目录是否保留。
# ls -al /var/spool/mail	/* 查看邮箱目录。

# useradd test_user001		
# userdel -r test_user001	/* 删除此用户及其家目录和邮箱。
# ls -al /home			/* 查看删除结果。
# ls -al /var/spool/mail
```

------

**passwd**

passwd [*OPTION* ...] [*username*]

update user's authentication tokens.

更新用户的身份验证令牌。

```
/* 锁定用户密码。通过在加密密码段前加感叹号使密码无效，但账号并未完全锁定，仍然可以通过 ssh 公钥方式进行身份验证。 手册页建议使用 chage -E 0 user 命令来完全锁定账号。
-l, --lock

/* 解锁账号。通过移除加密密码前的感叹号来实现。如果加密密码段里只有一个感叹号，就需要同时使用 -f 选项来解锁。
-u, --unlock

-f, --force		/* 强制特定操作，如 -u。

/* 查看账户状态。对应与账户在 /etc/shadow 中字段的信息。第二个字段为 LK、NP 和 PS 之一，分别代表账号锁定、无密码和有密码。
-S, --status

--stdin			/* 从输入读取密码。

--d, --delete		/* 删除账号的密码。

-n, --minimum DAYS	/* 设置密码更改的最小间隔天数。
-x, --maximum DAYS	/* 设置密码更改的最大间隔天数。
-w, --warning DAYS	/* 设置提前警告用户密码过期天数。
-i, --inactive DAYS	/* 设置密码过期后多少天禁用该账户。
-e, --expire		/* 账户密码立刻过期，下次登录必须修改密码。
```

示例：

```
# userdel -r test_user001	/* 删除账户。
# useradd test_user001		/* 添加账户。
# passwd -S test_user001	/* 查看账户信息。
test_user001 LK 2021-01-14 0 99999 7 -1 (Password locked.)
# passwd test_user001		/* 设置 test_user001 账户密码。重复输入两次。
# passwd -S test_user001	/* 设置密码后再次查看账户信息。
test_user001 PS 2021-01-14 0 99999 7 -1 (Password set, SHA512 crypt.)

# login	test_user001	/* 以 login 方式登录 test_user001，检测密码设置。
$ exit				/* 退出，登录 root。

# echo "password" | passwd --stdin test_user001
/* --stdin 通常用于在脚本中大批量设置初始密码。

# passwd -l test_user001	/* 锁定账户。
# passwd -S test_user001	/* 查看账户信息。
test_user001 LK 2021-01-14 0 99999 7 -1 (Password locked.)

# passwd -u test_user001	/* 解锁账户。
# passwd -S test_user001	/* 查看账户信息。
test_user001 PS 2021-01-14 0 99999 7 -1 (Password set, SHA512 crypt.)
```

注：设定账户信息在 /etc/shadow 文件中 4-8 字段的选项，请自行测试。

------

**usermod**

usermod [*OPTION* ...] *LOGIN*

modify a user account.

修改用户账号。

```
-a, --append		/* 添加指定附加组，仅与 -G 一同使用。

/* 可以用逗号分隔的组列表来为用户指定多个所属组，如果用户原所属组不在此列表中，则会被删除。可以同时使用 -a 选项使其只添加组列表所指定的组。
-G, --group [GROUP1[,GROUP2,...[,GROUPN]]]

/* 更改用户初始组为指定组。用户家目录及其内所有文件的所属组也会更新，但家目录外的文件就只能手动修改了；另外作为一种安全措施，如果用户 ID 与指定的所属组 ID 不同，则也不会修改。
-g, --gid GROUP

/* 仅在用户账户信息中设置指定家目录信息。如果还给定 -m 选项，原家目录中的内容会移动到新目录。如果原家目录不存在，则不会创建新的家目录。
-d, --home HOME_DIR

/* 此选项仅和 -d 选项同时使用才有效。原家目录中的内容会移动到新目录。如果原家目录不存在，则不会创建新的家目录。usermod 会更新文件的所有权、访问权限、访问控制列表及扩展属性，另外还可能需要手动操作。
-m, --move-home

/* 指定账号过期时间，日期格式为 YYYY-MM-DD。等同 chage -E 0 user_account。
-e, --expiredate EXPIRE_DATE
```

示例：

```
# useradd example_user001		/* 创建新用户
# useradd example_user002		/* 同上

/* 为用户 example_user002 添加附属组 example_user001。
# usermod -a -G example_user001 example_user002
# cat /etc/group			/* 查看组信息。
...
example_user001:x:1006:example_user002
example_user002:x:1007:
...

# usermod -e 0 example_user002		/* 禁用账号。
# grep example_user002 /etc/shadow	/* 查看账号过期字段是否设置为 0。
example_user002:$6$D9LA8eU48iWbjEpz$GByrBn3L7N9mGkeeV1:18641:0:99999:7::0:
```

------

**groupadd**

groupadd [*OPTION* ...] *group*

create a new group.

创建一个新组。

```
-r, -system			/* 创建一个系统组。
-g, --gid GID			/* 创建新组并指定 GID。
```

示例：

```
# groupadd -r system_group	/* 创建系统组。
# cat /etc/group		/* 检查所创建的组。

# groupadd -g 1128 spe_id_group	/* 添加指定 GID 为 1128 的普通组。
# cat /etc/group
```

------

**groups**

groups *USERNAME*

print the groups a user is in.

输出用户所在组。

示例：

```
# useradd sysadmin		/* 添加一个用户。
# usermod -aG wheel sysadmin	/* 为指定用户添加系统组。
# groups sysadmin		/* 查看用户的所有组。
sysadmin wheel
# groups			/* 查看执行命令的用户的所有组。
root
```

------

**groupdel**

groupdel [*OPTION* ...] *GROUP*

delete a group.

删除一个组。

示例：

```
# groupdel spe_id_group		/* 删除指定的组。
# cat /etc/group		/* 检查指定组是否删除。
```

------

**chown**

chown [*OPTION* ...] [*OWNER*][:[*GROUP*]] *FILE* ...
chown [*OPTION* ...] --*reference*=*RFILE* *FILE* ...

change file owner and group.

更改文件的属主和所属组。

```
/* 只更改符号链接所指向文件的所属权，而不是链接文件自身。chown 命令默认行为。
--dereference
/* 只更改符号链接自身的所属权，而不是符号链接指向的文件。
-h, --no-dereference

/* 引用 RFILE 文件的所属权作为参照，来更改指定文件的所属权。
--reference=RFILE

-R, --recursive		/* 递归处理。
	递归处理时如果选择多个以下子选项，则最后一个生效：
	-H	/* 如果命令行参数是一个指向目录的符号链接，则遍历。
	-L	/* 遍历遇到的每一个符号链接指向的目录。
	-P	/* 不遍历符号链接指向的目录。默认行为。
```

示例：

```
/* 递归更改指定文件的所属权为 root，但符号链接指向的文件的所属权不受影响。
# chown -hR root:root /anyuserfiles
```

------

#### 文件权限管理相关命令

------

**chmod**

chmod [*OPTION* ...] *MODE* *FILE* ...
chmod [*OPTION* ...] --*reference*=*RFILE* *FILE* ...

change file mode bits.

修改文件模式位。

```
--R, --recursive	/* 递归处理。

/* 引用 RFILE 文件的权限作为参照，来更新目标文件的权限。
--reference=RFILE
```

chmod 权限修改格式表

| 所有权类型  |  修改操作符 | 权限符号表示法 | 权限八进制符号表示法 |
|  -------- | -------- | -----   | ------ |
| u = user  |    +     |  r | 4  |
| g = group |    -     |  w | 2  |
| o = other |    =     |  x | 1  |
| a = all   |          |  - | 0  |

注： 或者以符合此正则的格式：`[ugoa]*([-+=]([rwxXst]*|[ugo]))+|[-+=][0-7]+`。其中 `Xst` 为特殊权限。


示例：

```
# chmod o+w Hello-Lucky		/* 对其他所有人添加可写权限。
# chmod o=rw Hello-Lucky	/* 效果同上。
# chmod a+w Hello-Lucky		/* 对所有人添加可写权限。
# chmod a+2 Hello-Lucky		/* 效果同上。

# chmod g-w Hello-Lucky		/* 对所属组减去可写权限。
# chmod o-w Hello-Lucky		/* 对其他所有人减去可写权限。
	
# chmod g-w,o-w Hello-Lucky	/* 等同以上两次命令的效果。

# chmod 644 Hello-Lucky		/* 直接设定文件的权限为上面显示的权限值。
# chmod ugo=rw-r--r-- Hello-Lucky		/* 效果同上。
```

示例：

```
/* 检验目录权限对其内文件的影响。

# useradd testuser01		/* 添加一个用户。
# passwd testuser01		/* 为其设置密码。

# mkdir /test_{r,w,x,rw,wx,rx}	/* 创建六个目录。

/* 在六个目录下分别创建一个文件。

# echo 'a file in a dir with read permission' >> /test_r/testr.txt
# echo 'a file in a dir with write permission' >> /test_w/testw.txt
# echo 'a file in a dir with execute permission' >> /test_x/testx.txt
# echo 'a file in a dir with read and write permission' >> /test_rw/testrw.txt
# echo 'a file in a dir with write and execute permission' >> /test_wx/testwx.txt
# echo 'a file in a dir with read and execute permission' >> /test_rx/testrx.txt

/* 分别更改这六个目录的所属权和文件模式位。

# chmod o=r test_r
# chmod o=w test_w
# chmod o=x test_x
# chmod o=rw test_rw
# chmod o=wx test_wx
# chmod o=rx test_rx

# login testuser01	/* 登录 testuser01

$ ll -d /test*		/* 查看目录属性。
$ ll /test*		/* 查看目录文件。

请自行尝试，看看哪些文件可以被 testuser01 用户修改。
```

------

#### 文件的特殊权限

setuid 权限

setuid 权限是让可执行程序的执行者临时提权到文件属主的权限，以访问原先没有权限访问的文件和进程。比如命令 passwd，任何人都可用此命令访问 root 才有权访问的 /etc/shadow 等文件来更改自己的密码。

权限赋予方式： `chmod u+s [execute_file]`

setgid 权限

setgid 权限赋予可执行文件，则可执行程序的执行者临时提权到文件所属组的权限。 如果赋予目录，则在此目录中创建的文件的所属组为目录的所属组，而不是默认的所属组。一般用于在多个用户之间共享目录，只要这些用户都属于同一个组。

权限赋予方式： `chmod g+s [execute_file | dir]`

sticky bit 权限

sticky bit 权限一般用与创建像目录 /tmp 同样权限 1777 的目录，任何人都可以在其中创建只有自己和 root 可以完全管理的目录和文件。也就是说，就算你具有目录写权限，但只有属主和 root 可以删除或改名其中的文件。

sticky bit 权限如果赋予文件，则即使文件的其他人具有所有权限，也无法删除此文件，但可以修改内容。

权限赋予方式： `chmod o+t [dir | file]`

注： 特殊权限位一般很少用，共享文件可以使用 NFS， SAMBA 服务，但你得知道这么回事，以解决未来可能会碰到的诡异权限问题。

------

#### Linux 文件扩展属性

Linux 定义了一组可以在文件上设置的扩展属性，以请求一些特殊的处理。但这些属性在不同的文件系统上并没有完全获得支持。

扩展属性标志解释表

| 标志  | 文件系统 |       解释         |
| ---- | ------- | -------------------- |
| a    |  XBE    | 只能追加。           |
| A    |  XBE    | 不更新访问时间。     |
| c    |  B      | 压缩。               |
| C    |  B      | 不写时复制。         |
| d    |  XBE    | 不做备份。           |
| D    |  BE     | 强制目录更新被同步写入。|
| e    |  XBE    | 使用块扩展存储文件。    |
| i    |  XBE    | 不可修改。              |
| j    |  E      | 数据先写入日志再写入文件。|
| s    |  XB     | 删除则用零填充，不可恢复。|
| S    |  XBE    | 强制同步，不写入缓冲。    |

注： X = XFS，B = Btrfs，E = ext3 和 ext4。

------

#### 文件扩展属性相关命令： chattr、lsattr

------

**chattr**

chattr [*OPTION* ...] +|- *attributes* *FILE*

change file attributes on a Linux file system.

更改文件扩展属性。

```
-R		/* 递归处理。
```

------

**lsattr**

lsattr [*OPTION* ...] *FILE*

list file attributes on a Linux second extended file system.

显示文件扩展属性。

```
-R		/* 递归处理。
```

注：同样要注意，如果某个文件很怪异，要用 lsattr 查一下。

------

#### 访问控制列表

访问控制列表（ACL）是一套类似传统九位权限模型的权限控制。当未显式对文件应用 ACL 时，ACL 权限信息与九位权限模型一致，但是当显式对文件设置 ACL 权限后，两套权限模式仅在属主和其他人的权限位保持一致，其它部分的权限将受到 ACL 权限的约束。另外，setuid、setgid 和 stickbit 此类特殊权限仍由传统的权限模式管理机制来处理。

| 格式                  | 权限符号 | 权限设置的对象 | 
| --------------------- | ------- | ------------- |
| user::perms           |    r    | 文件的属主    |
| user:username:perms   |    w    | 指定的用户    |
| group::perms          |    x    | 文件的所属组  |
| group:groupname:perms |    -    | 指定的所属组  |
| other::perms          | other::--- | 其他人       |
| mask::perms           | mask::rwx  | 除属主和其他人之外的所有人 |

注: mask 值类似 umask 值的功能，但它只动态反映九位权限模型与 ACL 中，除属主和其他人之外的所有人的最高权限。

**setfacl**

setfacl [*OPTION* ...] *file* ...

set file access control lists.

设置文件的访问控制列表。

```
-m, --modify		/* 设置 ACL 权限。
-x, --remove		/* 取消指定用户或组的 ACL 权限。

-R, --recursive		/* 递归处理。

-b, --remove-all	/* 移除指定文件的 ACL 权限 。

-M, --modify-file	/* 从指定文件读取 ACL 权限，批量设置权限。
-X, --remove-file	/* 从指定文件读取 ACL 权限，批量取消权限。
```

**getfacl**

get file access control lists.

获取文件的访问控制列表。

示例：

```
# useradd ex_user		/* 创建一个用户。
# mkdir /test		/* 创建一个目录，使其他用户可在其内创建文件。
# chmod a+w /test
# su --login ex_user		/* 切换到指定用户。

/* 创建一个文件。
$ echo 'a test file' >> /test/acl_test.txt
$ exit				/* 退出用户，并返回到 root。

# ll /test/acl_test.txt
-rw-rw-r--. ex_user ex_user 12 Jan 16 19:10 /test/acl_test.txt
# getfacl /test/acl_test.txt	/* 查看文件 ACL 初始状态。
# file: test/acl_test.txt
# owner: ex_user
# group: ex_user
user::rw-
group::rw-
other::r--

/* 修改文件的权限模式位，然后查看 ACL 权限是否与权限模式位保持一致。
# chmod g-w /test/acl_test.txt
# ll /test/acl_test.txt
-rw-r--r--. ex_user ex_user 12 Jan 16 19:18 /test/acl_test.txt
# getfacl /test/acl_test.txt
# # file: test/acl_test.txt
# owner: ex_user
# group: ex_user
user::rw-
group::r--
other::r-- 

/* 添加其他用户。
# useradd bob
# useradd bryan

/* 设置文件的访问控制列表，属主只读、bob 可读写、bryan 组可读写。
# setfacl -m user::r,user:bob:rw,group:bryan:rw /test/acl_test.txt

/* 查看文件的访问权限，设置 ACL 后，ACL 标识位为加号。
# ll /test/acl_test.txt
-r--rw-r--+ 1 ex_user ex_user 12 Jan 16 21:20 /test/acl_test.txt

/* 查看文件 ACL 的访问权限。ACL 的访问权限反映了设置结果，并且包含了 mask 值。
# getfacl /test/acl_test.txt
# file: test/acl_test.txt
# owner: ex_user
# group: ex_user
user::r--
user:bob:rw-
group::r--
group:bryan:rw-
mask::rw-
other::r--

/* 使用传统权限模式更改权限，以查看两套权限模式对应情况。
# chmod 760 /test/acl_test.txt
# ll /test/acl_test.txt
-rwxrw----+ 1 ex_user ex_user 12 Jan 16 21:20 /test/acl_test.txt

# getfacl /test/acl_test.txt
# file: test/acl_test.txt
# owner: ex_user
# group: ex_user
user::rwx
user:bob:rw-		/* 两套权限不一致处。
group::r--		/* 两套权限不一致处。
group:bryan:rw-		/* 两套权限不一致处。 
mask::rw-		/* 不一致处的最高权限。
other::---
/* 从以上对比，可以看到除用户和其它人之外的所有人的权限是不一致的，那么这部分人的权限将受到 ACL 的约束。
```

注：据闻使用 ACL 的不多。但如果碰到诡异的权限问题，也可以查查。

------

**su**

su [ - | --*login* ] *user*

run a command with substitute user and group ID.

使用替换的用户和组 ID 运行命令。

如果未指定任何参数，则默认 root 用户，su 默认不改变当前目录，仅设置环境变量 HOME、SHELL、USER、LOGNAME。

如果使用 `- | --login` 则类似于使用指定用户登录。建议使用 `--login` 选项，以避免混合环境造成的副作用。

注：root 用户切换为普通用户不用输入密码。

示例：

```
# useradd example_user01          /* 添加一个用户。
# su --login example_user01       /* 以类似登录方式切换到指定用户。
```

------

**sudo**

sudo 可以定义哪些用户可以在哪些主机上运行哪些命令集或软件集，而且可以在不具有 root 用户密码的前提下来运行命令，每次 sudo 都会记录到相关日志。sudo 策略配置文件为 /etc/sudoers，必须用 visudo 来编辑。

/etc/sudoers 策略配置文件内容：

```
## Host Aliases
## Groups of machines. You may prefer to use hostnames (perhaps using 
## wildcards for entire domains) or IP addresses instead.
# Host_Alias     FILESERVERS = fs1, fs2
# Host_Alias     MAILSERVERS = smtp, smtp2

/* 定义主机集合：  Host_Alias    主机集合名称1 = 主机1,主机2,...

## User Aliases
## These aren't often necessary, as you can use regular groups
## (ie, from files, LDAP, NIS, etc) in this file - just use %groupname 
## rather than USERALIAS
# User_Alias ADMINS = jsmith, mikem

/* 定义用户集合：  User_Alias    用户集合名称1 = 用户1,用户2,...

## Command Aliases
## These are groups of related commands...

/* 定义命令集合：  Cmnd_Alias    命令集合名称1 = 命令1,命令2,...
/* 命令都是以完整路径出现的，是为了避免用户以 root 用户运行自己的程序或脚本。

## Networking
# Cmnd_Alias NETWORKING = /sbin/route, /sbin/ifconfig, /bin/ping, /sbin/dhclient, /usr/bin/net, /sbin/iptables, /usr/bin/rfcomm, /usr/bin/wvdial, /sbin/iwconfig, /sbin/mii-tool

## Installation and management of software
# Cmnd_Alias SOFTWARE = /bin/rpm, /usr/bin/up2date, /usr/bin/yum

## Services
# Cmnd_Alias SERVICES = /sbin/service, /sbin/chkconfig, /usr/bin/systemctl start, /usr/bin/systemctl stop, /usr/bin/systemctl reload, /usr/bin/systemctl restart, /usr/bin/systemctl status, /usr/bin/systemctl enable, /usr/bin/systemctl disable

## Updating the locate database
# Cmnd_Alias LOCATE = /usr/bin/updatedb

# Cmnd_Alias LOCATE = /usr/bin/updatedb

## Storage
# Cmnd_Alias STORAGE = /sbin/fdisk, /sbin/sfdisk, /sbin/parted, /sbin/partprobe, /bin/mount, /bin/umount

## Delegating permissions
# Cmnd_Alias DELEGATING = /usr/sbin/visudo, /bin/chown, /bin/chmod, /bin/chgrp 

## Processes
# Cmnd_Alias PROCESSES = /bin/nice, /bin/kill, /usr/bin/kill, /usr/bin/killall

## Drivers
# Cmnd_Alias DRIVERS = /sbin/modprobe

# Defaults specification

/* 默认环境配置。

#
# Refuse to run if unable to disable echo on the tty.
#
Defaults   !visiblepw

#
# Preserving HOME has security implications since many programs
# use it when searching for configuration files. Note that HOME
# is already set when the the env_reset option is enabled, so
# this option is only effective for configurations where either
# env_reset is disabled or HOME is present in the env_keep list.
#
Defaults    always_set_home
Defaults    match_group_by_gid

# Prior to version 1.8.15, groups listed in sudoers that were not
# found in the system group database were passed to the group
# plugin, if any. Starting with 1.8.15, only groups of the form
# %:group are resolved via the group plugin by default.
# We enable always_query_group_plugin to restore old behavior.
# Disable this option for new behavior.
Defaults    always_query_group_plugin

Defaults    env_reset
Defaults    env_keep =  "COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS"
Defaults    env_keep += "MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE"
Defaults    env_keep += "LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES"
Defaults    env_keep += "LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE"
Defaults    env_keep += "LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY"

#
# Adding HOME to env_keep may enable a user to run unrestricted
# commands via sudo.
#
# Defaults   env_keep += "HOME"

Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin

## Next comes the main part: which users can run what software on 
## which machines (the sudoers file can be shared between multiple
## systems).
## Syntax:
##
##      user    MACHINE=COMMANDS
##
## The COMMANDS section may have other options added to it.
##
## Allow root to run any commands anywhere 
root    ALL=(ALL)       ALL

/* 上行的意思为，root 可以在所有的主机运行所有的命令。
/* 哪些用户可以在哪些主机运行哪些命令或软件。


## Allows members of the 'sys' group to run networking, software, 
## service management apps and more.
# %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS

## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL

/* 上行的意思为，wheel 组的用户可以在所有的主机运行所有的命令。

## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

/* NOPASSWD: 不需求密码。非常不建议如此设置。

## Allows members of the users group to mount and unmount the 
## cdrom as root
# %users  ALL=/sbin/mount /mnt/cdrom, /sbin/umount /mnt/cdrom

## Allows members of the users group to shutdown this system
# %users  localhost=/sbin/shutdown -h now

## Read drop-in files from /etc/sudoers.d (the # here does not mean a comment)
#includedir /etc/sudoers.d

/* sudo 总是服从所有匹配到最后一行，也就是说，如果匹配条目有冲突，总是后面的起作用。
```

注：sudoers 配置很复杂，但一般只要把管理员账号添加到 wheel 组就好了，配置都不用改就能满足大多数要求。

示例：

```
# useradd -r -g wheel sysacc_01       /* 创建一个属于 wheel 组系统用户。
# passwd sysacc_01                    /* 设置密码。
# su --login sysacc_01                /* 切换到系统用户。
# sudo cat /etc/passwd                /* 查看 root 用户权限文件。
/* wheel 组成员默认设置为可以使用 root 所有命名。
```

注：学到这个命令后，就要开始养成习惯使用 sudo 来替代使用 root 用户来管理了。

------
