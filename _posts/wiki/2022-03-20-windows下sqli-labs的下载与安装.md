Hello大家好，这里是KOKO师傅~

今天带领大家搭建sqli-labs靶场！

# 什么是sqli-labs

sqli-labs是由一名印度程序员写的，用来学习sql注入的一个`闯关游戏`，相当于练习用的一个靶场。

# sqli-labs下载与安装

## 环境

`apache+mysql+php`

Windows下可以直接用phpstudy,wamp等直接安装，Linux也有很多其他教程，本篇文章用phpstudy为基础。

phpstudy：[http://phpstudy.php.cn/](http://phpstudy.php.cn/)
sqli-labs-master：[https://github.com/Audi-1/sqli-labs](https://github.com/Audi-1/sqli-labs)

## 安装、配置

将sqli-labs解压到phpstudy的`WWW`目录下：
![str1](https://img-blog.csdnimg.cn/41e4a4e1c3344977a792a32592a9b395.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


在`sqli-labs-master/sql-connections`里有个`db-creds.inc`文件，打开后对账号和密码进行修改：
![str2](https://img-blog.csdnimg.cn/475c7cc28e9d49feb344c6d84c9502e5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![str3](https://img-blog.csdnimg.cn/df4f8393d42540b6a06aa8efa2c50b83.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


之后启动phpstudy，开启apache和Mysql。
在浏览器中进行访问：`localhost/sqli-labs-master`，点击`Setup/reset Database for labs`安装数据库：
![str4](https://img-blog.csdnimg.cn/758b222a5d0d454985ec6585aa5b6c80.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


安装成功后为下图：
![str5](https://img-blog.csdnimg.cn/1a019389411347d5abcecc5e8c576fcb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


返回上一页，点击`SQLI-LABS Page-1`后，看到如下图所示，即为安装成功，至此靶场便搭好了：
![str6](https://img-blog.csdnimg.cn/0b7f04b806d449f28059b902f871cb61.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![str7](https://img-blog.csdnimg.cn/a13e73fc320248799cab15e66a42837e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)



