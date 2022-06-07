用戶分類和組

/etc/passwd   保存所有用戶信息

```
root:x:0:0:root:/root:/bin/bash
bin :x:1:1:bin :/bin :/sbin/nologin
username 
密碼佔位符，密碼被移到/etc/shadow保存
userId(uid, 0 super user 500-60000 normal user 1-499程序用戶)
groupId(gid, 先有組，才有用戶)
用戶信息信息記錄字段
用戶家目錄
用戶登錄後的命令解釋器： nologin說明次用戶不能登錄系統

```

/etc/shadow 保存了用戶密碼

```
root:$6$kqlky75R$G...:18975:0:99999:7:::
bin:*:18353:0:99999:7:::

username
password: sha + salt
距離1970/1/1密碼最近一次修改時間
密碼的最短有效期 0不限制 3表示3天內不能修改
密碼最長有效期，距離1970/1/1日天數
密碼過期前7天警告
密碼不活躍期
用戶失效時間

```

/etc/group

```
root:x:0:
bin:x:1:

```

建立組

```
groupadd group1 # add group group1
groupmod -g 1001 group1 # modify group1's id to 1001
groupadd -g 2000 group2 # add group2 with id 2000
useradd -g group1 tom # add a user tom to group1
usermod -G group2 -u 600 tom # add tom to additional group2 with uid 600
useradd -g group1 -G group2 700 jerry
useradd -u 250 -M -s /sbin/nologin testuser # add app user can't login
id testuser # show testuser info
passwd tom # super user add passwd for tom
chage -M 90 tom # change password max valid day to 90 days
passwd -l tom # lock tom 
passwd -S tom # show passwd info 
passwd -u tom # unlock tom
userdel -r tom # del tom and tom's home dir
group del group1 #del group1

```

權限

文件或目錄屬於那個用戶那個組，不同的用戶能對該文件進行何種操作

```
ls -l init-org.sh
-rwxr-xr-x 1(hard link number) root(owner) root(owner group) 16872(size) Dec 14 15:23(last modify time) init-org.sh
ls -a resource
d rwx rwx rwx 4(sudirectories) root root      4096 May 25  2021 resource
#########
d rwx rwx rwx
文件類型 普通 d目錄 l鏈接符號 b塊設備
文件所屬這對文件的權限 
r w x
文件： read write excute
目錄： 查看目錄  增刪文件 進入目錄
文件所屬組權限
其他用戶權限
. 說明文件受SE-Linux保護

```

權限修改

```
chmod 用戶對象 算數運算符 權限 文件
用戶對象： u：user g: group  o: other a: all
算數運算符：- 去除權限 + 增加權限 = 指定權限
權限： rwx
chmod o-r test.sh #收回其他用戶的讀取權限

chown tom /tmp/test.txt #修改文件的所屬者爲tom
chgrp tom /tmp/test.txt #修改組


```

權限的八進制表示

000 ---   001 --x   010 -w- 011 -wx 100 r--  101 r-w 110 rw- 111 rwx

記住三個： 001 --x        010  -w-   100 r--

```
chmod 764 test.txt # rwx rw- r--
```

粘滯位 sgid suid 權限

粘滯位針對目錄賦權 目錄種創建的文件只有建立者可以刪除

mkdir test

chmod o+t test #設置粘滯位

ls -l /

drwxrwxrwt.  10 root root  4096 Jan 24 15:39 tmp #tmp文件具有粘滯位

sgid針對目錄建立的權限，在該目錄中建立的文件所屬組繼承父目錄的所屬組

chmod g+s test

ll -d test

drwxrwsrwt. ..........test 

suid 對可執行文件建立，運行文件者具有該文件所屬者的權限！

#### 不允許添加新的用戶請求

```
chattr +i /etc/passwd /etc/shadow # change file immutable
lsattr /etc/passwd /etc/shadow
----i--------e-- /etc/passwd
----i--------e-- /etc/shadow
chattr -i /etc/passwd /etc/shadow # cancel
```

#### umask

```
   umask() sets the calling process's file mode creation mask
   (umask) to mask & 0777 (i.e., only the file permission bits of
   mask are used), and returns the previous value of the mask.
```



#### Find

```
find /user/bin -perm 4755 #按照權限查找

```



超級管理員

普通用戶

