---
layout: post
title: Android进程管理机制及优化
category: 技术
tags: process Management
keywords: Android ,process ,Management
description: Android进程管理机制及优化 （转载）
---


>Android进程管理机制及优化 各种收集与总结

### 理论
	
   Android采取了一种有别于Linux的进程管理策略，有别于Linux的在进程活动停止后就结束该进程，Android把这些进程都保留在内存中，直到系统需要更多内存为止。这些保留在内存中的进程通常情况下不会影响整体系统的运行速度，并且当用户再次激活这些进程时，提升了进程的启动速度。
        那Android什么时候结束进程？结束哪个进程呢？之前普遍的认识是Android是依据一个名为LRU（last recently used 最近使用过的程序）列表，将程序进行排序，并结束最早的进程。XDA的楼主又进一步对这个管理机制进行研究，有了如下发现：
        系统会对进程的重要性进行评估，并将重要性以"oom_adj" 这个数值表示出来，赋予各个进程；（系统会根据"oom_adj"来判断需要结束哪些进程，一般来说，"oom_adj"的值越大，该进程被系统选中终止的可能就越高）
        前台程序的"oom_adj"值为0，这意味着它不会被系统终止，一旦它不可访问后，会获得个更高的"oom_adj"，作者推测"oom_adj"的值是根据软件在LRU列表中的位置所决定的；
   Android不同于Linux，有一套自己独特的进程管理模块，这个模块有更强的可定制性，可根据"oom_adj"值的范围来决定进程管理策略，比如可以设定“当内存小于X时，结束"oom_adj"大于Y的进程”。这给了进程管理脚本的编写以更多的选择。

### Android将进程分为六大类：

* 前台进程（foreground）：目前正在屏幕上显示的进程和一些系统进程。举例来说，Dialer Storage，Google Search等系统进程就是前台进程；再举例来说，当你运行一个程序，如浏览器，当浏览器界面在前台显示时，浏览器属于前台进程（foreground），但一旦你按home回到主界面，浏览器就变成了后台程序（background）。我们最不希望终止的进程就是前台进程。

* 可见进程（visible）：可见进程是一些不再前台，但用户依然可见的进程，举个例来说：widget、输入法等，都属于visible。这部分进程虽然不在前台，但与我们的使用也密切相关，我们也不希望它们被终止（你肯定不希望时钟、天气，新闻等widget被终止，那它们将无法同步，你也不希望输入法被终止，否则你每次输入时都需要重新启动输入法）

* 次要服务（secondary server）：目前正在运行的一些服务（主要服务，如拨号等，是不可能被进程管理终止的，故这里只谈次要服务），举例来说：谷歌企业套件，Gmail内部存储，联系人内部存储等。这部分服务虽然属于次要服务，但很一些系统功能依然息息相关，我们时常需要用到它们，所以也太希望他们被终止

* 后台进程（hidden）：虽然作者用了hidden这个词，但实际即是后台进程（background），就是我们通常意义上理解的启动后被切换到后台的进程，如浏览器，阅读器等。当程序显示在屏幕上时，他所运行的进程即为前台进程（foreground），一旦我们按home返回主界面（注意是按home，不是按back），程序就驻留在后台，成为后台进程（background）。后台进程的管理策略有多种：有较为积极的方式，一旦程序到达后台立即终止，这种方式会提高程序的运行速度，但无法加速程序的再次启动；也有较消极的方式，尽可能多的保留后台程序，虽然可能会影响到单个程序的运行速度，但在再次启动已启动的程序时，速度会有所提升。这里就需要用户根据自己的使用习惯找到一个平衡点

* 内容供应节点（content provider）：没有程序实体，进提供内容供别的程序去用的，比如日历供应节点，邮件供应节点等。在终止进程时，这类程序应该有较高的优先权

* 空进程（empty）：没有任何东西在内运行的进程，有些程序，比如BTE，在程序退出后，依然会在进程中驻留一个空进程，这个进程里没有任何数据在运行，作用往往是提高该程序下次的启动速度或者记录程序的一些历史信息。这部分进程无疑是应该最先终止的。

### 实践

	说完理论，说些实践的东西，怎样管理这六类进程，如何来设置进程管理模块是这部分说的内容。

	首先是软件，推荐使用MinFreeManager，市场上就有下载，用于设置这六类进程的管理策略。

	软件运行后有六个输入框，在输入框中只能输入数字，这些数字代表了这类进程的处理策略，
	比如Foreground App下的输入框显示6，就表示，当可用内存低于6MB时，终止Foreground App。下面的类似，如Empty App下的输入框显示24，则表示，当内存低于24MB时，终止Empty App。

	从软件数值的设置不难看出结束进程的有限顺序：Empty>Content Provider>Hidden>Secondary Server>Visible>Foreground。

### 但默认设置确存在一些问题：

各类进程的管理策略的阀值相当接近：6，8，16，20，22，24，最大的相差也不到8MB，在实际程序运行中，很容易导致多种类型的进程同时被关闭。如可用内存在25时，突然启动照相程序，系统可用内存急速，可能会导致空进程、内容供应节点、后台进程、次要服务等同时被关闭

阀值上限较低：一般手机启动后，可用内存在50-100左右，但随着手机的使用，可用内存会逐步减少，最后降低到24MB左右，则系统开始启动进程管理机制，开始结束进程，但这个阀限制设在了24MB，相对来说偏低。其结果会导致系统使用一段时间后，整体速度变慢。很明显的就是，当手机长时间使用后，开启电话拨号，相册，照相机等应用时，系统的反应速度极慢。
	
基于以上几个问题，不难看出，我们修改的目标也将非常明确，主要解决两个矛盾：

* 拉开各进程的阀值层次，使得进程管理机制能更有效得工作

* 提升阀值上限，空出更多的空余内存，以提升系统整体的运行速度

### 进程管理策略设置原则：
	
前台进程、可见进程和次要服务是与用户体验息息相关的内容，这部分的进程管理策略要相对保守，给这些进程留下足够的运行空间压榨无用进程，腾出内存空间给主要程序使用
	
下面笔者总结了几种设置方式，适应不同的使用需要：
	
- 游戏玩家/重度浏览器使用者配置：
	用户特点：长时间专注于某一特定的，高内存需求的程序，对多任务的需求不高
	配置参数：
	
```
	Foreground：6
	Visible：8
	Secondary Server：16
	Hiden App：80
	Content Provider：90
	Empty:100
```
	配置理念：压榨后台进程，内容供应节点和空进程，将内存尽可能多得留给前台进程和系统，提升游戏速度和浏览器体验
	优点：程序启动和运行的速度最快
	缺点：多任务处理不理想，开启程序较多时，后台进程会被终止

- 多任务配置：
	用户特点：同时运行多个应用程序，需要经常在多个程序间切换
	配置参数：

```
	Foreground：6
	Visible：8
	Secondary Server：16
	Hiden App：20
	Content Provider：60
	Empty:100

```
	配置理念：压榨空进程，给内容供应节点留有一定空间，最大限度提升后台程序的使用空间，提升多任务的处理能力
	优点：运行多个程序时，由于可支配内存较多，后台程序不容易被终止
	缺点：程序启动的速度和整体系统的运行速度可能会比游戏玩家配置略慢一些，由于经常运行多任务，平时系统的响应速度会受到一定影响

- 轻度用户/女生专用配置
	用户特点：手机的主要功能是短信和电话，偶尔用用相机自拍
	配置参数：
	
```
	Foreground：6
	Visible：8
	Secondary Server：16
	Hiden App：24
	Content Provider：32
	Empty:48

```
	配置理念：压榨空进程，给内容供应节点留有一定空间，最大限度提升后台程序的使用空间，提升多任务的处理能力
	优点：比较均衡的配置，提升了系统的可用内存，使得系统的整体速度得到了提高，拉开了各级进程的管理策略层次，使得管理机制更有效率
	缺点：比较均衡的配置，无明显缺点

### 总结

   阐述完了内存管理的机制，并推荐了一些配置参数，但这些参数并不一定适用于所有人，大家也可以根据自己实际的使用习惯调整这些参数的设置。

### 而下面要研究的就是这个Android内部的lowmemorykiller.

ActivityManagerService.java 记录着每一个进程的优先级. 一个进程的oom_adj值也就代表了它的优先级. 

oom_adj值越高代表该进程优先级越低. 一个正在使用的进程的oom_adj值为0,一旦我们按下返回键,这个进程就会得到一个更高的oom_adj值(更低的优先级). 

具体多少取决于该进程在LRU(last recently used) list的位置.(未证实) 具体的细节保存在Android源文件drivers/misc/lowmemorykiller.c里.

Android将进程分为6个等级,它们按优先级顺序由高到低依次是:

```
    1. FOREGROUND_APP:
    This is the process running the current foreground app.  We'd really rather not kill it!
    用户正在使用的程序. 这个设的太高,用户看到得就会是一个正在使用的程序莫名其妙的消失了,然后自动回到桌面..(因为它被系统kill了..) 所以最好别动它..
    2. VISIBLE_APP:
    This is a process only hosting activities that are visible to the user, so we'd prefer they don't disappear.8 b2 ]  ]; p5 N; S
    跟FOREGROUND_APP类似,用户正在使用/看得到. 它们的区别就是VISIBLE_APP可能不是用户focus的程序,但是用户看得到,或者没有覆盖到整个屏幕,只有屏幕的一部分. 所以可以适当的比FOREGROUND_APP高一点.
    3. SECONDARY_SERVER:
    This is a process holding a secondary server -- killing it will not have much of an impact as far as the user is concerned.
    所有应用的service. 系统级的service比如PhoneService不属于这类,它们是绝不会被Android结束掉的. 所以这个可以适当的设高一点点~ 注意, HOME(SenseUI)也包括在这里 因此还是别设的太高. 要不每次返回桌面都得等它重新load,特别是widget多的.
    4. HIDDEN_APP:
    This is a process only hosting activities that are not visible, so it can be killed without any disruption.
    本来属于1或者2的程序, 在用户按了"back"或者"home"后,程序本身看不到了,但是其实还在运行的程序,它们就属于HIDDEN_APP了. 干掉没什么影响.. 不过要了解并不是所有属于这一类的就应该马上结束掉,像push mail,locale,闹钟,等都属于这一类. 因此还是别设的过高. 真正"应该"一点返回键就退出的程序(真正没用的程序)在下面.
    5. CONTENT_PROVIDER:
    This is a process with a content provider that does not have any clients attached to it.  If it did have any clients, its adjustment would be the one for the highest-priority of those processes.
    5,6的区别具体不太了解..这个也是用处不大,但是还是比EMPTY_APP稍微有点用.. 所以高点没关系~
    6. EMPTY_APP:
    This is a process without anything currently running in it.  Definitely the first to go! This value is initalized in the constructor, careful when refering to this static variable externally.

```

查看现在的设置可以:

1. 

```
 cat /sys/module/lowmemorykiller/parameters/minfree
 
```
显示出的应该是6个数字,以逗号隔开,例如:

```
1536,2048,4096,5120,5632,6144

```

注意这些数字的单位是page.  1 page = 4 kilobyte.;

上面的六个数字对应的就是(MB): 6,8,16,20,22,24


这些数字也就是对应的内存阀值,一旦低于该值,Android便开始按顺序关闭进程. 因此Android开始结束优先级最低的EMPTY_APP当可用内存小于24MB(6144*4/1024).

有一点没搞明白,它的可用内存不知道是从哪得到.. 明显不是free显示的可用内存,而且貌似compcache跟swap也不影响..

要想重新设置该值:

```

   1. # echo "1536,2048,4096,5120,15360,23040" > /sys/module/lowmemorykiller/parameters/minfree
   
```

这样当可用内存低于90MB的时候便开始结束EMPTY_APP. 而当可用内存低于60MB的时候才开始结束CONTENT_PROVIDER组. 其余四个没动.

### 注意:

通过以上方法改变的数值并非永久.在下次重启后就又恢复到之前的设置. 若想让设置在每次开机执行,将

```
	1. echo "1536,3072,4096,21000,23000,25000" > /sys/module/lowmemorykiller/parameters/minfree

```

加入到任意一个开机启动的配置文件. 一般在/system/init.d下的文件都是开机执行的(有的ROM也可能不在这里..) 只需用记事本打开任意一个文件,再把这行加入其中就好.

原作者做了一个应用,在market里免费,让大家更改设置更方便,叫 "MinFreeManager".

上面给出的数字只供参考,具体多少才是最优组合正在研究中..

也许大家还不明白这样做的好处. 这样的好处就是让我们随时有足够的内存来执行我们要运行的程序,而那些真正没用的进程又不会多余的占用着宝贵的内存. 更重要的是这一切都是不需要您的参与或任何第三方软件的协助,完全由Android在后台自动执行. 试想,又有谁会比Android更熟悉的掌握每个进程呢 比起那些内存管理程序傻傻的一锅端的方法聪明多了吧~ 让我们从现在开始把那些内存管理程序删掉吧!