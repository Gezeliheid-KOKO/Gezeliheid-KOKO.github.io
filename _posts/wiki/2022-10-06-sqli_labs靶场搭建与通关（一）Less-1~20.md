大家好，这里是KOKO~

上篇文章将SQL注入讲的透透彻彻！
这篇带大家搭建一下SQL注入最常用的靶场`sqli-labs`，并直接通关Less-1~20！！！

@[TOC](Sqli-labs靶场)

# 一、靶场搭建

必备环境：`PHPStudy`

`源码`：[https://github.com/Audi-1/sqli-labs](https://github.com/Audi-1/sqli-labs)

**将下载好的压缩包解压到PHPStudy的`WWW`目录下：**![1](https://img-blog.csdnimg.cn/0d69a4c762424fe6a8889e5903b9e14c.png#pic_center)
**进入目录，找到`sql-connections`目录进入：**
![2](https://img-blog.csdnimg.cn/2c1fb00ad8664b87b4dd56772e9b434f.png#pic_center)
**编辑`db-creds.inc`文件，这里主要是编辑`$dbpass=''`：**
![3](https://img-blog.csdnimg.cn/6a72d11883d1474f9bc913b2e6ec0b0c.png#pic_center)
![4](https://img-blog.csdnimg.cn/8dcb45b8411849acb8742274e1f943b6.png#pic_center)
**然后在PHPStudy中更换`php版本`，换成`php5.4`：**
![5](https://img-blog.csdnimg.cn/be16d558d69b4ae5bd312ac297c19bb4.png#pic_center)
**之后访问网站即可成功看到靶场页面：**
![6](https://img-blog.csdnimg.cn/a9ad559c8f80475fae8e4eaa99651a0e.png#pic_center)
**点击`Setup/reset Database for labs`如果出现以下页面，则代表搭建成功：**
![7](https://img-blog.csdnimg.cn/cb4091bb602543d2b07e153f2fa66a64.png#pic_center)
**接下来就可以开始我们的注入之旅啦！**
![8](https://img-blog.csdnimg.cn/b56c240414574e65bcb1887734921103.png#pic_center)
# 二、靶场实录
## Less-1
![9](https://img-blog.csdnimg.cn/57b4449aa7d34cdc92dbb9675d80eee3.png#pic_center)
提示输入数字值的ID作为参数，我们多传几个参`id=1/2/3/...`看看会有什么效果：
![10](https://img-blog.csdnimg.cn/89260dd7ac97401bb53fa2a668ea3729.png#pic_center)
![11](https://img-blog.csdnimg.cn/02dbe4aa00cf4b5598b1d41cd3e1766d.png#pic_center)
可以发现当我们传不同的参时会出现不同的结果，因此我们输入的内容是带入到数据库里面查询了。

接下来我们判断sql语句的注入类型：
![12](https://img-blog.csdnimg.cn/1594a605eba7433795eb630de7b68905.png#pic_center)

我们可以看到加上`'`后出现了报错，且框起来的地方我们可以发现就是我们输入的值，通过观察页面的报错提示可以发现我们的输入值其实是由两个单引号包裹的，初步判断注入类型为`单引号注入`。

接下来，在id=1' 后加上 "- - +"即注释符号可将注释符号后的语句全部注释，这样我们就可以在注释符号前写入sql语句，使用order by语句判断数据库列数
>`order by n：`当n小于等于真实列数时，页面不报错。当n大于真实列数时，页面报错

![13](https://img-blog.csdnimg.cn/cf2c9e32ed87486f9ecb209683b7c5cd.png#pic_center)
![14](https://img-blog.csdnimg.cn/14065dc57c6046468795f796d596be37.png#pic_center)
根据报错情况我们可知，数据库共有3列。

接下来我们使用`联合查询`爆出`显示位`，就是看看表格里面哪一列是在页面显示的：
>注意，此时要使前部语句条件为假才能成功查询，可以使用`id=-1`也可以使用`id=1 and 0`，只要能使前部语句条件为假即可

![15](https://img-blog.csdnimg.cn/0ff513c10077484c9a7fbd3c4a192931.png#pic_center)
可以看到是第二列和第三列的数据显示在页面上。

接下来我们尝试获取当前数据库名和版本号：
>因为第一列不显示在页面上，因此第一个值赋个数值即可，后面两列用函数来查询

![16](https://img-blog.csdnimg.cn/2a6f290672ff467fac2ac9d898734198.png#pic_center)
通过结果知道当前数据库名是`security`,版本是`5.7.26`。

接下来我们尝试**爆表** ：

我们查询`information_schema`数据库下的`tables`表里面且`table_schema`字段内容是`security`的所有`table_name`的内容。
>其中
>table_name为表名；
>information_schema为数据库；
>tables用于存储information_schema数据库下所有的表名；
>table_schema代表数据库名；
>database()指具体的数据库名；
>group_concat函数:查询所有；

![17](https://img-blog.csdnimg.cn/fe09adf4c73043329521046ebefae8ca.png#pic_center)
我们通过sql语句查询知道当前数据库有四个表，根据表名知道可能用户的`账户`和`密码`是在`users`表中。

接下来我们就是爆出该表下的字段名以及内容：

我们查询`information_schema`数据库下的`columns`表里面且`table_users`字段是`users`的所有`column_name`的内容。

>注意`table_name`字段不是只存在于`tables`表，也是存在`columns`表中。表示所有字段对应的表名。

![18](https://img-blog.csdnimg.cn/53b929dc46df48dc8bd2ac04378ad4a9.png#pic_center)
通过上述操作可以得到两个敏感字段就是`username`和`password`,接下来我们就要得到该字段对应的内容。

我们可以加一个`id`用来将账户和密码隔开：
![19](https://img-blog.csdnimg.cn/84a440df075744feb089c970c65769a7.png#pic_center)
至此，第一关通关！

## Less-2
我们先和第一关一样传参的时候加上`'`或者`"`试一下会有什么报错：
![20](https://img-blog.csdnimg.cn/bf155ee20afc4cbeabded1385ce32ace.png#pic_center)
可以看到和第一关不同的是这次报错没有出现我们输入的数字。

所以我们猜测是数字型注入。

接下来和第一关的步骤差不多：
**首先我们先爆出有几列：**
![21](https://img-blog.csdnimg.cn/860715d68ff241049a88b047eecec455.png#pic_center)
![22](https://img-blog.csdnimg.cn/66a47c8ca5ed488db791ebaa8bb3cf9f.png#pic_center)
可以看出仍然是3列！
**接下来我们爆出页面上显示的是第几列：**
![23](https://img-blog.csdnimg.cn/bea86d37324143d4bca181ac1498a88b.png#pic_center)
可以得到仍然是第2、3列！
**接下来我们爆出数据库和版本号：**
![24](https://img-blog.csdnimg.cn/79c183c366fd4470b967d15e8ac86b4f.png#pic_center)
数据库名和版本号已经爆出！
**接下来我们尝试爆出表名：**
![25](https://img-blog.csdnimg.cn/e353b838175740d091c7d637c4de1897.png#pic_center)
我们得到了表名！
**接下来，我们尝试获取敏感信息，敏感信息应该在`users`表中，因此我们先尝试爆出表中所有列：**
![26](https://img-blog.csdnimg.cn/2eaf0ea76b4649b1a2f8893463356963.png#pic_center)
可以看出主要的敏感信息就是`username ,id , password`这三列！
**接下来我们对敏感信息进行爆破：**
![27](https://img-blog.csdnimg.cn/c3bf7afb93784c94ae4b40c066965fcc.png#pic_center)
至此，第二关通关！

## Less-3
我们再次尝试一下`'`的报错信息会给我们带来什么提示：
![28](https://img-blog.csdnimg.cn/33acb0f685804ed6b0a65cce18aaa846.png#pic_center)
根据报错信息可以得知是`单引号注入`，而且还有个括号，说明我们还要闭合括号：
![29](https://img-blog.csdnimg.cn/93837fd7518a447c9121376786ecb81a.png#pic_center)
经过实践证明我们的猜测是正确的。
**接下来的操作和前两关相似。**
**爆出几列：**
![30](https://img-blog.csdnimg.cn/11e7f81331db4bd1a8d9462907ce408b.png#pic_center)
仍旧是只有3列。
**接下来爆出显示列：**
![31](https://img-blog.csdnimg.cn/b624fd40ed9b4388a5b1bf6ed69b690c.png#pic_center)
根据回显可以发现依旧是2、3列作为显示列。
**接下来爆出数据库和版本号：**
![32](https://img-blog.csdnimg.cn/95df1053b36c467d82e5988b98086a48.png#pic_center)
成功爆出数据库名和版本号！
**接下来爆出表名：**
![33](https://img-blog.csdnimg.cn/e7adb3b7089c4afebc555092dc759446.png#pic_center)
得到表名，敏感信息应该依旧在`users`表中。
**接下来爆出`users`表中所有列：**
![34](https://img-blog.csdnimg.cn/48b454a2927f4bc18a0fd8348f34bf70.png#pic_center)
我们可以看到敏感信息的那几列。
**接下来爆出敏感信息：**
![35](https://img-blog.csdnimg.cn/c4657f74405245b8a6db45f1b7fc84dc.png#pic_center)
至此，第三关通关！

## Less-4
这次我们加`'`发现仍然可以正常输出，因此我们尝试一下`"`：
![37](https://img-blog.csdnimg.cn/af385223048f4493936ff491007673d8.png#pic_center)
根据报错信息可以得出，这次是`双引号注入`，并且还有个括号需要闭合。
**尝试出注入类型后，其余爆破步骤和前3关一样：**
![38](https://img-blog.csdnimg.cn/0499eaec90d74ed3a8cc772131da825c.png#pic_center)
![39](https://img-blog.csdnimg.cn/6aec8a600dd742f4a22e5cac2572c12f.png#pic_center)
![40](https://img-blog.csdnimg.cn/ecb2ce5b394e4cedada655ae00efd3df.png#pic_center)
![41](https://img-blog.csdnimg.cn/d124521d6c324648ad264d3d66decb02.png#pic_center)
![42](https://img-blog.csdnimg.cn/a2c987033c5342899ddffa56e5f8002c.png#pic_center)
![43](https://img-blog.csdnimg.cn/ea17fe7e468348538fc58d078d3b3eb2.png#pic_center)

至此，第四关通关！

## Less-5
![44](https://img-blog.csdnimg.cn/3eb0c1c1533d4e7eac4ffd458b7e692a.png#pic_center)
我们进行传参，发现页面与前几关并不相同。

页面虽然有东西，但是没有了回显位，因此我们无法再使用联合查询。

如果数据只有对错页面显示我们可以选择布尔盲注。

我们验证一下是哪种盲注：
![49](https://img-blog.csdnimg.cn/bb529718a20c43a48036ef90915b3da4.png#pic_center)
输入上图的语句后，发现页面出现了明显的5秒延迟，因此我们可以判断是时间盲注！

**首先，我们先判断数据库名的长度：**

![45](https://img-blog.csdnimg.cn/2460b56060e04873a5d6171b33a1a254.png#pic_center)
![46](https://img-blog.csdnimg.cn/4968cf967f404b1b99b0ea79e687a1cc.png#pic_center)
由传参后的延迟时间我们可以判断，数据库名长度为8。

**接下来我们爆数据库名的字母分别是什么：**

![47](https://img-blog.csdnimg.cn/1defd26d40fc408f8cffde723244a6ef.png#pic_center)
![48](https://img-blog.csdnimg.cn/1c884ed5b6a041bbb3c8f0f3ee3ae196.png#pic_center)
由传参后页面延迟时间可以判断，数据库名的第1位是`s`。

数据库名剩下字母的测试同上。

**接下来我们爆表名：**
![50](https://img-blog.csdnimg.cn/10c940c1f84448f8a909f72248bca990.png#pic_center)
由页面的延迟可知表名为`users`。

**接下来爆列名：**
![51](https://img-blog.csdnimg.cn/cdb4b3d5d3d14ff39c36756636f9f446.png#pic_center)
我们修改`limit x,1`中的`x`查询`password`是否存在表中。
我们在`limit 5,1`的时候通过页面的延迟确定了password列。

**同样的方法查询username ，接下来爆破字段的值：**
![52](https://img-blog.csdnimg.cn/cbf84aebfa5443e3bb80c3d824b4b219.png#pic_center)
![53](https://img-blog.csdnimg.cn/1973be4dd2df40cea18b67976a56a3b7.png#pic_center)
按照id排序，这样便于对应。
>注意`limit`从`0`开始。

通过坚持不懈的尝试终于爆破到第一个用户的名字dumb，密码dumb。
需要注意的是，MySQL对大小写不敏感，所以不知道是Dumb还是dumb。

至此，第五关通关！

## Less-6
![54](https://img-blog.csdnimg.cn/049b9652abe84cb18761a3246b71f0aa.png#pic_center)
根据页面报错信息可知是`双引号字符错误`。

因此只需要将第5关的payload中的`'`改成`"`即可：
![55](https://img-blog.csdnimg.cn/394d66e5dda14e2097a9f15cb40cf625.png#pic_center)
![56](https://img-blog.csdnimg.cn/fb850c5212514938bdb8b6eb1ba0ed67.png#pic_center)
![57](https://img-blog.csdnimg.cn/0d1ddc61ce1e4549b62c70ca64f7430f.png#pic_center)
![58](https://img-blog.csdnimg.cn/816fbde5a4d941c3859ad8cb621fa4da.png#pic_center)
![59](https://img-blog.csdnimg.cn/d867ec58041d4d359ec57071a30d58af.png#pic_center)
![60](https://img-blog.csdnimg.cn/40caabc14f0d466c861ee9cd9bde2f32.png#pic_center)
![61](https://img-blog.csdnimg.cn/1e93031a35d4444a9688e712ff3be39b.png#pic_center)
至此，第六关通关！

## Less-7
标题为`Dump into outfile`即利用`文件导入`的方式。
不论注入方式是什么，我们都要先判断注入点：
![62](https://img-blog.csdnimg.cn/caa640527d9f437abf4fe4d83415a3d6.png#pic_center)
**我们写入一句话木马到anbei.php文件中，anbei.php是自动新建的：**
![63](https://img-blog.csdnimg.cn/092b4d1933914f90869e912279e63bf4.png#pic_center)
![64](https://img-blog.csdnimg.cn/977e20a8812a405393a329e0b4ecc06a.png#pic_center)
**接下来用蚁剑连接木马：**
![65](https://img-blog.csdnimg.cn/804127836d204f4190f3302ce9d74cf0.png#pic_center)
至此，第七关通关！

## Less-8
![66](https://img-blog.csdnimg.cn/17eb7e6f8b564af894913a7da3fbc333.png#pic_center)
第八关和第五关一样，使用第五关的方法即可！

至此，第八关通关！

## Less-9
第九关会发现我们不管输入什么，页面显示的东西都是一样的。
这个时候布尔盲注就不适合，布尔盲注适合页面对于错误和正确结果有不同反应。如果页面一直不变这个时候我们可以使用时间注入，如果页面回显有延迟则说明注入成功。

**爆参数构造：**
![67](https://img-blog.csdnimg.cn/1f59c39154444f41a55f8810f7bcde31.png#pic_center)
**爆库名长度：**
![68](https://img-blog.csdnimg.cn/cc73cf3bea8d46c68205844c98293f72.png#pic_center)
**逐一爆数据库字符：**
![69](https://img-blog.csdnimg.cn/78ef6822cef2407abee824aea4f40b2d.png#pic_center)
**爆所有表名长度：**
![70](https://img-blog.csdnimg.cn/f0d9f3ea27a441f3bad6ee8ea62f293d.png#pic_center)
**逐一爆表名：**
![71](https://img-blog.csdnimg.cn/c3a9efa9d6cb4b06bf164adc5b80493e.png#pic_center)
**爆所有字段名的长度：**
![72](https://img-blog.csdnimg.cn/66e7aa54b2bf4e11b203c754de84f061.png#pic_center)
**逐一爆字段名：**
![73](https://img-blog.csdnimg.cn/395b0f73e83446ee89b668cc5c699feb.png#pic_center)
**爆字段内容长度：**
![74](https://img-blog.csdnimg.cn/6e36dceade364f30bee637e8df559419.png#pic_center)
**逐一检测内容：**
![75](https://img-blog.csdnimg.cn/ac8bcc0424be41eba644d21c75635d22.png#pic_center)
至此，第九关通关！

## Less-10

第十关和第九关步骤相同，只需要将单引号换成双引号即可。

至此，第十关通关！

## Less-11
![76](https://img-blog.csdnimg.cn/7d58e88066ea4bc991386661445dab88.png#pic_center)
当我们输入`1'`,出现报错信息。
根据报错信息可以推断该sql语句：`username='参数' and password='参数'`。

知道sql语句我们可以构造一个恒成立的sql语句，看可以查询出什么。

>这里我们使用--+注释发现不行，需要换成#来注释。

剩下的就第一关是一样的。使用**联合注入**就可以获取数据库信息：

![77](https://img-blog.csdnimg.cn/dcf5dded98c9482eb7278b0787a7746f.png#pic_center)
![78](https://img-blog.csdnimg.cn/9dcfd802f0a3414c974c71f7fcccd8b0.png#pic_center)
至此，第十一关通关！

## Less-12
![79](https://img-blog.csdnimg.cn/6ac2c3cbee0c4b4885bf57994432ec59.png#pic_center)
当我们输入`1`和`1'`时，页面并没有回显什么错误信息。

![80](https://img-blog.csdnimg.cn/bbb18750026e4687bb39d043be39056b.png#pic_center)
当我们输入`1"`的时候页面出现报错信息，因此我们可以得知sql语句是双引号且有括号。

**接下来我们判断是否存在SQL注入：**
![81](https://img-blog.csdnimg.cn/49606d53fa8140ab9c0581718f2a4ae0.png#pic_center)
**接下来和第一关一样使用联合查询即可：**

![82](https://img-blog.csdnimg.cn/2d3297d83546435b833b7d133cac963f.png#pic_center)
至此，第十二关通关！

## Less-13
十三关和十二关是同样的方法，把双引号换成单引号即可。

至此，第十三关通关！

## Less-14
十四关和十一关是同样的方法，把单引号换成双引号即可。

至此，第十四关通关！

## Less-15
第十五关和第十一关一样，只是不产生错误信息。

**十五关是很明显的布尔盲注，因为有错误页面和正确页面用来参考：**

![83](https://img-blog.csdnimg.cn/d0f7292fb0464ca9b5d773185677a221.png#pic_center)
![84](https://img-blog.csdnimg.cn/1cb69a75b8f04478ac47fb1bb0662583.png#pic_center)
至此，第十五关通关！

## Less-16
第十六关和第十二关是一样的，进行布尔盲注。

![85](https://img-blog.csdnimg.cn/cd9bf0d419ab4fad804ce8a39bb0aada.png#pic_center)
至此，第十六关通关！

## Less-17
第十七关和前面的关有很大不一样，根据页面展示是一个密码重置页面，也就是说我们已经登录系统了。

**查看源码：**

![86](https://img-blog.csdnimg.cn/0bd8c02c58224d6b93e0a64beddd948c.png#pic_center)
sql语句之前都是查询，这里有一个`update`更新数据库里面信息。
所以之前的联合注入和布尔盲注以及时间盲注都不能用了。
这里我们会用到报错注入：

**updatexml报错注入：**
`UPDATEXML (XML_document, XPath_string, new_value)`
**第一个参数：** XML_document是String格式，为XML文档对象的名称
**第二个参数：** XPath_string (Xpath格式的字符串) 
**第三个参数：** new_value，String格式，替换查找到的符合条件的数据
`作用：`改变文档中符合条件的节点的值，改变XML_document中符合XPATH_string的值
当我们XPath_string语法报错时候就会报错。

**爆库：**
![87](https://img-blog.csdnimg.cn/18429916f7c04149b291a8480c13e3fe.png#pic_center)
**爆表名：**
![88](https://img-blog.csdnimg.cn/01b541a8ed434de2bbecc897df10ac6f.png#pic_center)
**爆列名：**

```sql
uname=admin&passwd=admin' and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='users' and column_name not in ('user_id','user','first_name','last_name','avatar','last_login','failed_login')),0x7e),1) --+ &submit=Submit
```

**爆值：**

```sql
uname=admin&passwd=11'  and  updatexml(1,concat(0x7e,(select password from (select password from users where username='admin') mingzi ),0x7e),1) --+&submit=Submit
```

至此，第十七关通关！

## Less-18
我们抓包后从`User-Agent`处进行注入，这里要注意语句的闭合：

![89](https://img-blog.csdnimg.cn/475e36fc68354706a15e3812ce9a56d6.png#pic_center)
**爆库：**

![90](https://img-blog.csdnimg.cn/ab142e946858423b9ba87a8db4cd4ca7.png#pic_center)
后续步骤类似。

至此，第十八关通关！

## Less-19

当我们输入正确的账户、密码时我们的`referer`字段内容会显示在页面上：

![91](https://img-blog.csdnimg.cn/207a3f676e184b54ba1bddf928bdfd6f.png#pic_center)
**爆库：**

```sql
'and extractvalue(1,concat(0x7e,(select database()),0x7e)) and '
```

剩下的类似上述语句，自行构造即可。

至此，第十九关通关！

## Less-20
代码中使用`Cookie`传递参数，但是没有对Cookie中传递的参数进行过滤操作。导致SQL注入漏洞的产生。

`cookie注入`：直接登录时抓包是抓不到cookie的，源码中可以看到我们的cookie是从uname中获取，刷新后才会执行select。

**爆行数：**

![92](https://img-blog.csdnimg.cn/171a905f3c8e44e2a5691f5289067855.png#pic_center)

接下来构造语句自行爆破：
```sql
'and updatexml (1,concat(0x5c,(select group_concat(username,password) from users),0x5c),1)#
```

至此，第二十关通关！