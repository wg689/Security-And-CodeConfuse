# Security-And-CodeConfuse
App Security And CodeConfuse (app 安全和代码混淆的demo的手把手教你的详细教程)

##📣本文内容
- 一)为什么要进行代码混淆
- 二)代码混淆的两种方法(①宏替换 ②脚本实现替换)[本文github地址](https://github.com/wg689/Security-And-CodeConfuse),敬请star
- 三)代码混淆参考的博客及说明


#📣1）为什么要进行代码混淆?

iOS 的任何app都可以使用classdump对原程序进行dump，可以dump出所有源程序的函数所有信息：源程序所有函数类型，变量全部泄露。这样的话，让攻击者，也就是黑客们了解了程序结构方便逆向。因为在工程中，我们这些变量或函数命名都是有一定可读性的，例如跟用户名相关的，那一般里面会有 userName，跟密码相关的一般会有 passWord，这样定义也是为了我们自己代码可读性更强，我们修改的时候才更加的方便。但是我们相信，这么个定义法，我们只是希望方便我们自己，我们可不希望方便黑客们去破解我们的APP。
如果我们把自己的程序打包为ipa 使用class-dump ,dump出程序的所有头文件可以看到所有的头文件,打开头文件就可以看到所有的变量和函数了:

![执行dump](http://upload-images.jianshu.io/upload_images/1194882-6487f7fcb35a5ed1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
代码没混淆反编译结果,.h文件中函数如下图所示
![代码没混淆的反编译结果](http://upload-images.jianshu.io/upload_images/1194882-98239dd199a5bb40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
代码混淆反编译结果,.h文件中函数如下图所示
![代码混淆后的反编译结果](http://upload-images.jianshu.io/upload_images/1194882-571fe68204d950f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**也许你会问,对微信和支付宝看看他们的头文件可不可以呢?当然也是可以的,具体怎么做 参考我的这篇博客[iOS 逆向导出app的头文件(逆向工程书籍补充)](http://www.jianshu.com/p/d2400a80fdc5)**,当然你也可以找更好的博客,看看微信的iOS开发人员函数命名是否规范,我看过,少数也是不规范的,大部分还是比较规范的!
为了阻止别人使用逆向工程修改我们的代码需要做反编译,微信的代码很多是没有反编译的。反向工程会带来许多问题，诸如知识产权泄露，程序弱点暴露易受攻击等。
很多人实现 [企业微信签到反编译](bbs.iosre.com/t/topic/4408), [用Hopper搞定Mac迅雷的会员以及离线下载功能](bbs.iosre.com/t/hopper-mac/1428)等等逆向工程的文章,这些逆向之所以成功,是因为这些微信和迅雷的实现这些功能的核心代码没有进行代码混淆,如果进行了代码混淆,即使逆向了,找不到实现功能的函数,无法修改这些函数,就无法实现用Hopper搞定Mac迅雷的会员以及离线下载功能了。
**为什么做混淆 ?还有更重要的原因**:某一天某安全公司给你发一份安全测试报告,你的代码没做混淆(当然不只这一个风险),存在安全风险,boss 听说有风险肯定让你解决这个问题啊,我们公司就是这样的情况,我才做的的代码混淆的!!
如何进行代码混淆,经过我查阅资料,发现有两种方法,一种简单较low,一种复杂点档次高点!下面介绍代码混淆的两种方法
#📣2代码混淆的两种方法

##2.1简单的宏替换（很简单也有一点点low）
####2.1.1
打开项目新建ConfuseReplace.h 文件,把需要替换的函数名使用#define就可以(本方法其实可以用这一句就可以说完的)
```
#ifndef ConfuseReplace_h
#define ConfuseReplace_h]

#define sample        XZwTFgszVxFieZta
#define seg1        mviuwjseJZYYamwu
#define zheshi     uNCrcYEobhpPGLuf
#define xxxxxx     achfhhffhhfh

#endif /* ConfuseReplace_h */

```
####2.1.2
controller.h文件中
```
@interface ViewController : UIViewController

-(void)sample;
-(void)seg1:(NSString *)string seg2:(NSUInteger)num;
-(void)zheshi:(NSString *)string seg2:(NSUInteger)num;

- (void)xxxxxx;

@end
```
controller.m文件中
```
-(void)sample{  
}

-(void)seg1:(NSString *)string seg2:(NSUInteger)num{
}
-(void)zheshi:(NSString *)string seg2:(NSUInteger)num{
   
}
- (void)xxxxxx{ 
}
```
**上面已经搞定了代码混淆,就是这么简单.每个函数名的串都是宏定义好的,也许有人会问,人家拿到了`ConfuseReplace.h`文件,那这么替换岂不是白搭,人家知道那个函数名和那个字符串对应啊,经笔者亲自验证,宏定义的字符串使用class-dump 是dump 不出来的,所以我们就放心的用**

我们使用代码混淆让别人使用class-dump 反编译出我们的方法名是乱码,达到混淆的目的,混淆之后黑客使用class-dump 出你的头文件不知道你这个函数是做什么,就不能修改你的功能 ,使用这种混淆方法class-dump 后的头文件如下,
使用class-dump需要先安装class-dump,如何安装class-dump见这篇文章[iOS逆向工程-----class-dump](www.jianshu.com/p/2add936e8bdd),有如何安装class-dump和使用class-dump
导出.h文件的过程如下图所示

![导出头文件替换的方式.gif](http://upload-images.jianshu.io/upload_images/1194882-14997a2242bc84d3.gif?imageMogr2/auto-orient/strip)

查看导出的头文件

![导出的头文件查看.gif](http://upload-images.jianshu.io/upload_images/1194882-57f0aa06ba1f2f4e.gif?imageMogr2/auto-orient/strip)


```
- (void)XZwTFgszVxFieZta;
- (void)achfhhffhhfh;
- (void)didReceiveMemoryWarning;
- (void)mviuwjseJZYYamwu:(id)arg1 seg2:(unsigned long long)arg2;
- (void)uNCrcYEobhpPGLuf:(id)arg1 seg2:(unsigned long long)arg2;
```
这种方法的demo见[我的github](https://github.com/wg689/Security-And-CodeConfuse)中的confuseDemo这个文件夹.其实说这么多**大家看一下Demo秒懂,唯一的难点是需要学习下class-dump导出头文件来验证是否混淆成功**!教程都给你链接了,学不学就看你啦!


##2.2使用脚本替换方法名（很简单也有一点点low）
第一、新建一个工程,在工程项目路径中建立一个confuse.sh、一个func.list文件先打开终端，然后 cd 到你的项目工程路径下：
![](http://upload-images.jianshu.io/upload_images/1194882-a83b24429d8a6f80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
cd 到项目工程路径下然后创建两个文件，一个 confuse.sh，一个 func.list：
![](http://upload-images.jianshu.io/upload_images/1194882-8882eaa1d6edcea3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**注意点,不按照下面两个操作,运行脚本会报错**:
![目录结构不要错](http://upload-images.jianshu.io/upload_images/1194882-5c76911e69387114.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![层级目录不要错](http://upload-images.jianshu.io/upload_images/1194882-41111738b3f3a338.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


创建两个文件这时候我们打开这个工程文件夹，可以看到，这两个文件已经创建好了：
![](http://upload-images.jianshu.io/upload_images/1194882-30d5ba6b57bc23f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们创建的两个文件打开工程，把刚才创建的两个文件加进去，右键你的项目蓝色标志，然后选择 Add Files to...：
![](http://upload-images.jianshu.io/upload_images/1194882-3647bfe4cff77928.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)添加到工程中

添加进去了：
![](http://upload-images.jianshu.io/upload_images/1194882-91c396eece9554c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
成功添加点击 confuse.sh**(confuse.sh,和func.list必须添加到NSUTest目录下)**发现还是空白的，什么都没有，现在要在这上面加上代码了：
![](http://upload-images.jianshu.io/upload_images/1194882-24898a7d0b1d8d95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
需要添加的代码原脚本代码出自：[http://blog.csdn.net/yiyaaixuexi/article/details/29201699](http://blog.csdn.net/yiyaaixuexi/article/details/29201699)
第二、在 .pch 文件中添加代码不要跟我说你的工程没有 .pch 文件，如果真没有，自行百度，这应该算是标配，常识来的。
```
#ifdef __OBJC__  #import <UIKit/UIKit.h> 
 #import <Foundation/Foundation.h>  
//添加混淆作用的头文件（这个文件名是脚本confuse.sh中定义的） 
 #import "codeObfuscation.h" #endif
```

写到这里，编译的时候是不是发现报错啦？刚才的 .pch 文件里面的添加的代码居然报错了：
![](http://upload-images.jianshu.io/upload_images/1194882-a00746901023d730.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
报错了！不要慌，先把那句报错的先给注释掉：
![](http://upload-images.jianshu.io/upload_images/1194882-99351d306a14f041.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
先注释掉报错的这一行代码然后我们继续往下走！
第三、配置 Build Phase1:添加 Run Script
![](http://upload-images.jianshu.io/upload_images/1194882-8785723ff48e33cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)添加 Run Script

2:配置好 Run Script
![](http://upload-images.jianshu.io/upload_images/1194882-27f3d3132dc61a6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
配置好 Run Script然后再回到终端，同样先 cd 到工程目录下，接着我们要打开刚才 .sh 这个脚本文件的运行权限，因为默认是没有这个权限的，在终端输入以下指令：
![](http://upload-images.jianshu.io/upload_images/1194882-589cbd5482842748.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打开运行权限回车，搞定，回到我们的工程，先 command + b 编译一下工程，然后再把我们刚刚注释掉的那句代码解开：
![](http://upload-images.jianshu.io/upload_images/1194882-57198c58d601b353.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打开刚刚被我们注释掉的代码再次 command + b 编译，现在是不是编译通过啦？刚刚报错的，现在解决了！
基本上就搞定了，剩下的就是添加上我们想要混淆的变量名或函数名
第四、在 func.list 文件里，写入待混淆的函数名如果像下面这几个属性跟函数：
![](http://upload-images.jianshu.io/upload_images/1194882-f15c04d03abca131.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
需要混淆的属性跟函数名那么就这在 fun.list 就这么列出来就好了：
![](http://upload-images.jianshu.io/upload_images/1194882-4ed4a4164857ef4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
列出需要混淆的,大功告成！现在 command + b 运行一下，然后在哪里看结果呢，请看这里：
![](http://upload-images.jianshu.io/upload_images/1194882-4aef5fd8b61407fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
运行结果可能这么看有点麻烦，那来个简单一点的：
![](http://upload-images.jianshu.io/upload_images/1194882-677a1c2664914bb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
查看哈哈，你会发现，多了好多宏定义，其实就是我们刚才的字段来的：
![](http://upload-images.jianshu.io/upload_images/1194882-be5aaa35dd378146.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
结果当然，这也只是最简单的代码混淆而已，APP安全还是有很多需要注意的。一步一步来吧！
**最后需要说明,出现下图所示的这里并不一定说明代码混淆成功,只能说明你的脚本运行成功,.pch文件中一定要导入`#import "codeObfuscation.h"`**最好对自己混淆的代码 打包成ipa进行dump,查看你混淆的头文件的函数名是不是随机的字符串了.
![Snip20160816_11.png](http://upload-images.jianshu.io/upload_images/1194882-beeb6c54243cc542.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##📣github地址和Demo中的各个文件夹用途说明
![demo文件目录](http://upload-images.jianshu.io/upload_images/1194882-4ca25fbf6a8aa48d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**对上图中的各个文件夹说明如下:**
- class dump header of confuseDemo:
里面放的是对confuseDemo(进行了混淆)打包为ipa,再执行class-dump,dump 出来的所有的头文件
- class dump header of Demo2  confuse success
里面放的是对Demo2(进行了混淆)打包为ipa,再执行class-dump,dump 出来的所有的头文件
- class dump header of Demo2 not confuse success
里面放的是对Demo2(没有进行混淆)打包为ipa,再执行class-dump,dump 出来的所有的头文件
- confuseDemo
使用宏定义替换的demo,直接参考,移植到自己的工程就行
- confuseDemo ipa
对confuseDemo 打包为ipa了,你可以使用里面的confuseDemo.app文件进行class-dump
- Demo2
使用脚本进行代码混淆的demo ,你可以参考,移植到自己的工程中

[本文github地址](https://github.com/wg689/Security-And-CodeConfuse),敬请star!
#本文参考博客:
[念茜女神的代码混淆博客](http://blog.csdn.net/yiyaaixuexi/article/details/29201699)脚本是她提供的
[iOS 对源代码进行混淆](www.jianshu.com/p/98227950a474)本文的部分内容在这篇的基础上更改的,原文只有一种方法,我这里提供了两种,原文没Demo,我提供了两种方法的Demo,对Class-dump等部分内容作了更多说明,站在别人的肩膀上一步步的优化完善的哈,才出来了本文!