
目录* [病毒背景](https://github.com)
* [样本分析](https://github.com)
	+ [开启监控](https://github.com)
	+ [感染病毒](https://github.com)
	+ [分析病毒行为](https://github.com)
		- [C盘文件监控](https://github.com)
		- [D盘文件监控](https://github.com)
		- [进程监控排查](https://github.com)
		- [服务排查](https://github.com)
		- [启动项排查](https://github.com)
	+ [查杀](https://github.com)
		- [1\.杀掉进程](https://github.com):[MeoMiao 萌喵加速](https://biqumo.org)
		- [2\.异常服务](https://github.com)
		- [3\.映像劫持处理](https://github.com)
		- [4\.hosts文件处理](https://github.com)
		- [5\.D盘文件删除](https://github.com)
		- [6\.其他异常排查](https://github.com)
	+ [重启排查](https://github.com)

# 病毒背景


**简介**：Win32\.PSWTroj.QQPass，名为：【QQ伪装盗号者】是一种QQ盗号木马，它会注入用户电脑的系统进程中运行，盗取病毒作者指定的帐号和密码以及其号码的其他信息。
参考链接：[https://baike.sogou.com/v126222\.htm](https://github.com)


# 样本分析


病毒主程序样本名字是servere.exe，双击他就会开始感染
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/cc2e0dd5402e4914a792ebb1a7f84d9a.png)


## 开启监控


用到两款工具，主要还是D盾的监控文件比较好用
1\.`MyMonitor`
工具链接分享：
[https://pan.baidu.com/s/1RKOR\_LvfNX8QqEyJaDFq1Q?pwd\=azwh](https://github.com)
2\.`D盾`
工具链接分享：
[https://pan.baidu.com/s/13hCSYpV5Mn\_1JMzy4nSkSQ?pwd\=kott](https://github.com)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2ebb90d69778409e867d888552d101e8.png)
`MyMonitor`打开即可，待会感染病毒需要将程序拖进去运行监控，然后将`D盾`监控文件夹，监控c盘和d盘，但是d盾只能一次监控一个文件夹，这里我就**主要监控c盘**，最后才补上监控d盘的操作。


## 感染病毒


`D盾`监控c盘功能打开后，就将程序文件拖进`MyMonitor`
等待程序运行推出即可看到报告，然后D盾也能看到具体操作了哪些文件
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d0d51089746f41bdb4a11d85ad9f8b8c.png)


## 分析病毒行为


**因为D盾一次只能监控一个盘符，所以分两次进行监控。我们使用虚拟机可以还原快照，所以多次监控也不算很麻烦，如果各位道友有发现更好的监控文件工具还请留言\~**


### C盘文件监控


`MyMonitor`简单看下就行，我感觉分类的不是很好，确实能看到操作，但是不能分类而且有的行为没有检测到，所以下面就围绕D盾来查看病毒到底做了些什么事情。
最明显就是创建了四个文件(看异常的主要的就行，像log后缀哪种日志可以忽略)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4d1a10151be64938b9d77511954ef558.png)



> **线索卡：**
> `1.c:\windows\system32\severe.exe`
> `2.c:\windows\system32\qvkwjh.exe`
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe`


往下看还有创建了dll文件，在这中间其实还有很多重复创建操作，这个我理解为病毒是为了防止程序没有创建成功所以多次创建得原因，病毒就这么写的。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e977bf9927a14e29a89db8e3e38234e9.png)



> **线索卡：**
> `1.c:\windows\system32\severe.exe`
> `2.c:\windows\system32\qvkwjh.exe`
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe`
> `5.c:\windows\system32\qvkwjh.dll`


往下看有一个动作是删除host文件然后创建了hosts文件，这个可以记录下来，后面需要去确认一下他对hosts文件做了什么手脚，一般可能是对域名进行ip替换之类的
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0e434f99b09d431abb1bf4c1032735d1.png)



> **线索卡：**
> `1.c:\windows\system32\severe.exe`
> `2.c:\windows\system32\qvkwjh.exe`
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe`
> `5.c:\windows\system32\qvkwjh.dll`
> `6.修改了c:\windows\system32\drivers\etc\hosts`


这里我双击d盘后创建了`hx1.bat`文件，具体行为是：`设置系统时间为2004-1-22`，因为后面有一个删除操作，文件不存在了就不找了，但是需要人为将时间设置回来。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a9d6dfc3928041e0baeaa9e61c1b49f5.png)



> **线索卡：**
> `1.c:\windows\system32\severe.exe`
> `2.c:\windows\system32\qvkwjh.exe`
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe`
> `5.c:\windows\system32\qvkwjh.dll`
> `6.修改了c:\windows\system32\drivers\etc\hosts`
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间`


### D盘文件监控


将虚拟机恢复快照，重新开启d盾监控d盘，然后重新感染一遍病毒，这里看监控结果就一目了然
`1.创建d:\oso.exe`
`2.创建d:\autorun.inf`
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/36aec2fa1d724d1abb81aff6087d3721.png)



> **线索卡：**
> `1.c:\windows\system32\severe.exe`
> `2.c:\windows\system32\qvkwjh.exe`
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe`
> `5.c:\windows\system32\qvkwjh.dll`
> `6.修改了c:\windows\system32\drivers\etc\hosts`
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间`
> `8.创建d:\oso.exe`
> `9.创建d:\autorun.inf`


这里省略将所有exe文件还有dll文件丢到沙箱上查一下，这里就放`jwbnlb.exe`沙箱运行的结果。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6bd4f74feda144d488239ef35c0498d1.png)


### 进程监控排查


打开我们的老朋友`PChunter`
工具链接：
[https://pan.baidu.com/s/1\_OMmoe5aFGDu3\-\-q0u94pw?pwd\=w3rb](https://github.com)\\


找到`servere.exe`进程，因为我们是双击这个程序感染的，当然也发现了病毒创建恶意程序文件`qvkwjh.exe`、`conime.exe`当做进程运行起来了。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4971b30b8d6d432b9c5b3e08144e19b6.png)
接着右键查看进程模块也发现了调用`qvkwjh.dll`模块\\



> **线索卡：**
> `1.c:\windows\system32\severe.exe`
> `2.c:\windows\system32\qvkwjh.exe`
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe`
> `5.c:\windows\system32\qvkwjh.dll`
> `6.修改了c:\windows\system32\drivers\etc\hosts`
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间`
> `8.创建d:\oso.exe`
> `9.创建d:\autorun.inf`
> `10.三个恶意进程：servere.exe、qvkwjh.exe、conime.exe`


### 服务排查


查恶意进程对应的服务，都没有发现有对应服务



```
tasklist /svc | findstr "PID"

```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/041c1498b14244669cc915e36aa299e0.png)
当然也要排查一下看有没有其他可疑的服务，着重看没有厂商签名的，这里就没发现有其他异常。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a7f9d19aeb95457f8826d6504ac13e71.png)


### 启动项排查


着重看没有厂商签名的启动项，果然发现三个异常的，而且还是对应了恶意文件路径的启动项
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ca6889bfa6974af2843f9e56f7697b66.png)
这里我正打算打开系统自带的注册表查看启动项相关信息且对其进行删除对应的值的时候，发现打不开
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9f9fad4cac5c4e2abb1cd37567072bbb.png)
尝试win\+r调出命令也不行，打不开
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/84594ae0fea5496487d01f5ce4eb7a90.png)
这里就意识到时中了`映像劫持了`：↓↓
解释：`就是Image File Execution Options（其实应该称为“Image Hijack”。）是为一些在默认系统环境中运行时可能引发错误的程序执行体提供特殊的环境设定。但是病毒可以故意将其一些常见的程序打开指向文件为不存在的或者自己指定的另外一些恶意程序文件，多次感染等等。举例子：将360杀软程序打开的动作指向了c:\windows\system32\servere.exe，那么这样不仅仅禁用了杀软还反复感染病毒了。`
解决办法：`将没有被禁用的程序辅助找到被禁用的程序文件，修改其名字即可，我这里就用文件搜索找到注册表文件，复制出来进行修改名字即可打开了。`
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4bebce728dc64fb2b14cd4f2abc4d078.png)
打不开注册表很明显就是被禁了，随便修改名字就能打开了
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9779089ed31249e6846066292b7992fb.png)
当然也可以通过PCHunter内部打开注册表，这里是不受影响的，因为没有禁用PChunter，但是为了尊重病毒制作者(滑稽)，还是要顺着他的意思走一下坑。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b858e7d0e8cb49b7becfe667f02607dc.png)
打开注册表后找到：
`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options`
就能发现这个目录项下面都是被禁掉的软件应用，指向的是：
`C:\WINDOWS\system32\drivers\jwbnlb.exe`
比如下图中360safe.exe就被ban掉了，若电脑上有用360safe.exe那么双击运行就会出发jwbnlb.exe运行，再次感染。（这个劫持挺不错，就是动静太大了）
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7004854a4085455b9adda394231144fc.png)
**映像劫持解决办法**：修改名字，删除注册表
`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options`中的被ban掉的目录项，查杀的时候将所有目录项删除掉即可，因为不是系统自带也不是人为创建的。



> **线索卡：**
> `1.c:\windows\system32\severe.exe`
> `2.c:\windows\system32\qvkwjh.exe`
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe`
> `5.c:\windows\system32\qvkwjh.dll`
> `6.修改了c:\windows\system32\drivers\etc\hosts`
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间`
> `8.创建d:\oso.exe`
> `9.创建d:\autorun.inf`
> `10.三个恶意进程：servere.exe、qvkwjh.exe、conime.exe`
> `11.启动项(启动项对应注册表)`
> `11.映像劫持：找到注册表 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options 该目录下的目录项删除即可`


## 查杀


### 1\.杀掉进程


使用PChunter勾选结束进程时候删除文件，那么就可以安心结束进程了，省掉手动找文件删除
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/56ea50337e2a446a83b76dbe6b428ca6.png)


忘记删除进程模块了，可以找到对应文件下进行删除，文件做了隐藏，所以还是需要通过PChunter找到文件删除：`c:\windows\system32\qvkwjh.dll`
最好先勾选删除后阻止文件再生，然后再次右键文件进行强制删除。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/017941772b7344e9a8d5be4884204a25.png)



> **线索卡：**
> `1.c:\windows\system32\severe.exe` 已删除
> `2.c:\windows\system32\qvkwjh.exe` 已删除
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe` 已删除
> `5.c:\windows\system32\qvkwjh.dll` 已删除
> `6.修改了c:\windows\system32\drivers\etc\hosts`
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间`
> `8.创建d:\oso.exe`
> `9.创建d:\autorun.inf`
> `10.三个恶意进程：servere.exe、qvkwjh.exe、conime.exe` 已结束
> `11.启动项(启动项对应注册表)`
> `11.映像劫持：找到注册表 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options 该目录下的目录项删除即可`


### 2\.异常服务


这三个异常服务都删除即可，`Shell启动项需要定位注册表删除`，这时候千万`不要直接打开系统的注册表`，使用`PChunter`打开，或者你自己搜索注册表出来`重命名`再打开注册表。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4dbc813a543a4d2da62bef6c8c768d15.png)
Shell启动项删除不掉，那就定位到注册表将其删掉即可
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f5635f457d074776af1e652ee04005d0.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ab0cc43baacd4fddb0c5e2754ce1fd27.png)



> **线索卡：**
> `1.c:\windows\system32\severe.exe` 已删除
> `2.c:\windows\system32\qvkwjh.exe` 已删除
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe` 已删除
> `5.c:\windows\system32\qvkwjh.dll` 已删除
> `6.修改了c:\windows\system32\drivers\etc\hosts`
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间`
> `8.创建d:\oso.exe`
> `9.创建d:\autorun.inf`
> `10.三个恶意进程：servere.exe、qvkwjh.exe、conime.exe` 已结束
> `11.启动项(启动项对应注册表)` 已删除
> `11.映像劫持：找到注册表 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options 该目录下的目录项删除即可`


### 3\.映像劫持处理


上面已经讲过了方法，**映像劫持解决办法**：修改名字，删除注册表
`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options`中的被ban掉的目录项，查杀的时候将所有目录项删除掉即可，因为不是系统自带也不是人为创建的。
我们的PChunter也能做，还能批量删除，比直接通过注册表一个个删除方便，同时还提供删除对应的程序文件，删除注册表和对应的程序文件。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a34a7633d38748babbdc2955e5ff8a69.png)


这时候再win\+r调出注册表就可以正常打开了
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/910580489eec4417b87411464762d524.png)
再次解释一下映像劫持的原理：通过在注册表`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options` 中添加对应的程序名字，打开的时候就会优先通过这注册表信息找到对应的程序路径，这里的程序路径写的是恶意程序文件，如果再次点击就会再次感染一遍。



> **线索卡：**
> `1.c:\windows\system32\severe.exe` 已删除
> `2.c:\windows\system32\qvkwjh.exe` 已删除
> `3.c:\windows\system32\drivers\jwbnlb.exe` 已删除
> `4.c:\windows\system32\drivers\conime.exe` 已删除
> `5.c:\windows\system32\qvkwjh.dll` 已删除
> `6.修改了c:\windows\system32\drivers\etc\hosts`
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间`
> `8.创建d:\oso.exe`
> `9.创建d:\autorun.inf`
> `10.三个恶意进程：servere.exe、qvkwjh.exe、conime.exe` 已结束
> `11.启动项(启动项对应注册表)` 已删除
> `11.映像劫持：找到注册表 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options 该目录下的目录项删除即可` 已处理


### 4\.hosts文件处理


正常文件夹中找不到该文件，被做了文件隐藏，那么就通过PChunter操作
发现文件内容为：(意思是屏蔽下列杀软等网站)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8adc15d5199d44a38a6c905512a19a80.png)



```
127.0.0.1       localhost
127.0.0.1       mmsk.cn
127.0.0.1       ikaka.com
127.0.0.1       safe.qq.com
127.0.0.1       360safe.com
127.0.0.1       www.mmsk.cn
127.0.0.1       www.ikaka.com
127.0.0.1       tool.ikaka.com
127.0.0.1       www.360safe.com
127.0.0.1       zs.kingsoft.com
127.0.0.1       forum.ikaka.com
127.0.0.1       up.rising.com.cn
127.0.0.1       scan.kingsoft.com
127.0.0.1       kvup.jiangmin.com
127.0.0.1       reg.rising.com.cn
127.0.0.1       update.rising.com.cn
127.0.0.1       update7.jiangmin.com
127.0.0.1       download.rising.com.cn
127.0.0.1       dnl-us1.kaspersky-labs.com
127.0.0.1       dnl-us2.kaspersky-labs.com
127.0.0.1       dnl-us3.kaspersky-labs.com
127.0.0.1       dnl-us4.kaspersky-labs.com
127.0.0.1       dnl-us5.kaspersky-labs.com
127.0.0.1       dnl-us6.kaspersky-labs.com
127.0.0.1       dnl-us7.kaspersky-labs.com
127.0.0.1       dnl-us8.kaspersky-labs.com
127.0.0.1       dnl-us9.kaspersky-labs.com
127.0.0.1       dnl-us10.kaspersky-labs.com
127.0.0.1       dnl-eu1.kaspersky-labs.com
127.0.0.1       dnl-eu2.kaspersky-labs.com
127.0.0.1       dnl-eu3.kaspersky-labs.com
127.0.0.1       dnl-eu4.kaspersky-labs.com
127.0.0.1       dnl-eu5.kaspersky-labs.com
127.0.0.1       dnl-eu6.kaspersky-labs.com
127.0.0.1       dnl-eu7.kaspersky-labs.com
127.0.0.1       dnl-eu8.kaspersky-labs.com
127.0.0.1       dnl-eu9.kaspersky-labs.com
127.0.0.1       dnl-eu10.kaspersky-labs.com


```

解决办法：拷贝出来，删除文件，从其他机器上拷贝一份好的过来用即可



> **线索卡：**
> `1.c:\windows\system32\severe.exe` 已删除
> `2.c:\windows\system32\qvkwjh.exe` 已删除
> `3.c:\windows\system32\drivers\jwbnlb.exe` 已删除
> `4.c:\windows\system32\drivers\conime.exe` 已删除
> `5.c:\windows\system32\qvkwjh.dll` 已删除
> `6.修改了c:\windows\system32\drivers\etc\hosts` 已处理
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间`
> `8.创建d:\oso.exe`
> `9.创建d:\autorun.inf`
> `10.三个恶意进程：servere.exe、qvkwjh.exe、conime.exe` 已结束
> `11.启动项(启动项对应注册表)` 已删除
> `11.映像劫持：找到注册表 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options 该目录下的目录项删除即可` 已处理


### 5\.D盘文件删除


首先你要注意`autorun.inf`文件的存在，如果有的话就不要随意双击盘符，还是右键打开盘符最好，很明显有一个坑， `（不要学我）我这里先双击打开`，然后查看进程你就会发现`又感染了一遍病毒`，所以一定要一定要谨慎一点，这个病毒还是比较狡猾的。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/28a2f51bd5d24a619439152c6ae26946.png)
回到正题，要删除d盘下的文件，你会发现都做了文件隐藏，所以还是继续上工具。
删除前打开autorun.inf查看你会发现还是老样子，果然OSO.exe文件是感染文件，如果你双击的话就会执行OSO.exe文件再次感染
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e9e2ab7feb114b91987bd1edeb3a585c.png)


接着PChunter找到文件进行删除即可
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bc92ef546c9a41a8b62ef00a9a5f12b6.png)


最后还剩下一个系统时间，这里就忽略了，默认已经处理。



> **线索卡：**
> `1.c:\windows\system32\severe.exe` 已删除
> `2.c:\windows\system32\qvkwjh.exe` 已删除
> `3.c:\windows\system32\drivers\jwbnlb.exe`
> `4.c:\windows\system32\drivers\conime.exe` 已删除
> `5.c:\windows\system32\qvkwjh.dll` 已删除
> `6.修改了c:\windows\system32\drivers\etc\hosts` 已处理
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间` 已处理
> `8.创建d:\oso.exe` 已删除
> `9.创建d:\autorun.inf` 已删除
> `10.三个恶意进程：servere.exe、qvkwjh.exe、conime.exe` 已结束
> `11.启动项(启动项对应注册表)` 已删除
> `11.映像劫持：找到注册表 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options 该目录下的目录项删除即可` 已处理


### 6\.其他异常排查


以下均没发现异常


* 异常连接排查（netstat \-ano）
* 异常账户排查（net user、克隆账户、隐藏账户）
* 计划任务
* 等等


## 重启排查


跟着线索卡走一遍看有没有再生文件，或者其他异常进程服务等等



> **线索卡：**
> `1.c:\windows\system32\severe.exe` 已删除
> `2.c:\windows\system32\qvkwjh.exe` 已删除
> `3.c:\windows\system32\drivers\jwbnlb.exe` 已删除
> `4.c:\windows\system32\drivers\conime.exe` 已删除
> `5.c:\windows\system32\qvkwjh.dll` 已删除
> `6.修改了c:\windows\system32\drivers\etc\hosts` 已处理
> `7.hx1.bat设置系统时间为2004-1-22，记得修改回来正确时间` 已处理
> `8.创建d:\oso.exe` 已删除
> `9.创建d:\autorun.inf` 已删除
> `10.三个恶意进程：servere.exe、qvkwjh.exe、conime.exe` 已结束
> `11.启动项(启动项对应注册表)` 已删除
> `11.映像劫持：找到注册表 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options 该目录下的目录项删除即可` 已处理


这里省略了入侵排查过程
1\.账号排查
2\.查看服务
3\.查看启动项
4\.查看计划任务
5\.网络情况
6\.进程排查
后续病毒没有做更多的动作，就是做了以上分析的操作，主要是为了盗取中毒的受害者的账号密码，早期qq风靡全球，所以盗取qq号最多，那时候经常有好兄弟给我发一些正能量，有时候分不清他们是真被盗了发的还是故意装被盗号发的，还好那时还小看不懂，太正能量了。




---


**qq巨盗病毒早期的盗号病毒，具体获取敏感数据这里没做分析，不过也是一次很好的应急响应历练，进程、启动项、服务、注册表、映像劫持、autorun、甚至修改hosts文件尽可能防止杀软程序进来把病毒杀了，用来提升应急思路还是不错的。**


