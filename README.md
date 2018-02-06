## 插件化开发
     随着业务复杂度增多，客户端不再是小巧的应用，这就给开发，维护，用户体验带来极差的效果，所以有了插件化开发

一、问题提出
     一般的，一个Android应用在开发到了一定阶段以后，功能模块将会越来越多，APK安装包也越来越大，用户在使用过程中也没有办法选择性的加载自己需要的功能模块。此时可能就需要考虑如何分拆整个应用了。
     当前问题：

          1.代码越来越庞大，很难维护
          2.需求越来越多，某一模块的小改动都要重新发布版本，发布时间越来越不可控。
          3.项目中某一段代码牵涉模块越来越多，应对bug反应越来越慢。

     潜在问题：
          早期的Dalvik VM内部使用short类型变量来标识方法的id，dex限制了程序的最大方法数是65535，如果超过最大限制，无法编译

二、解决方案提出

     1.用Html5代替部分逻辑
     2.删除无用代码，减少方法数
     3.插件化，将一个 App 划分为多个插件（Apk 或相关格式）

     前两种方法在某种条件下可以解决问题，但是治标不治本。下面我们重点说一下Android插件化开发。

三、Android插件化

     Android 插件化 —— 指将一个程序划分为不同的部分，比如一般 App 的皮肤样式就可以看成一个插件
     Android 组件化 —— 这个概念实际跟上面相差不那么明显，组件和插件较大的区别就是：组件是指通用及复用性较高的构件，比如图片缓存就可以看成一个组件被多个 App 共用
     Android 动态加载 —— 这个实际是更高层次的概念，也有叫法是热加载或 Android 动态部署，指容器（App）在运⾏状态下动态加载某个模块，从而新增功能或改变某⼀部分行为  

     在java开发中随处可见使用jar包的插件机制进行开发，但在android中，目前较成熟的插件机制基本没有，看到的帖子中提到了重写dexclassloader可以完美的解决插件问题，但都只简要描述了原理，没有源码或关键代码，下面对网络中的思路进行总结．

     目前结合插件包的格式来说插件的方式只有三种：１，apk安装，２，apk不安装，３，dex包．三种方式其实主要是解决两个方面的问题：１，加载插件中的类，２，加载插件中的资源．第一个加载类的问题，这三个方式都可以很好的解决．但目前三种方式都没有很完美的解决第２个问题．

     １，apk安装方式．插件apk安装后，可以在主程序中通过包名加载到插件的context,有了插件的context就可以解决加载插件资源的问题．但出现的新问题是：如果插件a,b,c间公用一个底层jar包，那么在abc间传送数据时，需要进行序列化和反序列化，因为a中jar包的data类与b中jar包的data类虽然都是同样的jar包也是同样的类，但两个类在java机制来是由不同的classloader加载的，是不同的类．那么就会出现插件间jar包冗余和数据传递的效率不好问题．总之能解决问题．

     ２，apk不安装,可以通过dexclassloader加载到插件a中的类，但插件没有安装就无法获得插件apk的context，也就无法加载到资源(可以通过代理的方式解决这个问题)

     ３，dex包，这个基本是java开发中jar包的方式．同样通过dexclassloader加载到插件中的类，但依旧没有context,也无法加载到资源．要使用布局，只能在插件中使用java代码来写布局；


四、优缺点

     优点：
     1.模块解耦
     2.解除单个dex函数不能超过 65535的限制
     3.动态升级
     4.高效开发(编译速度更快)

     基于插件的开发列举两个比较突出的优点：
     1、应用程序非常容易扩展，比如一个新的领域要加到旧的应用程序中，只需把这个新的领域做为一个插件，只开发这个小的app就可以了，旧的应用程序可能会原分不动，就连编译打包都不需要。
     2、下载更新特别省流量，假如一个应用程序有10M把它分成两个的话可能每次更新只需要花费5M或者更少的流量就可以更新完。
     
     缺点：
     1.增加了主应用程序的逻辑难度
     2.技术有难度，目前一些成熟的框架都是闭源的

五、技术实现

    1.只加载Dex：
        可以单独打包出一个dex，使用dx工具，这个dex值包括Class  文件，体积小；

    2.加载APK，这样就同时加载了Class和资源文件；

    3.测试加载成功例子：
            1.反射调用一下接口
            2.调用资源（字符串, 图片，布局，颜色，尺寸）
            3.插件中日志可以打印
    4.启动一个Activity
    5.给插件Activity添加完整生命周期
    6.获取插件所有Activity信息和包信息
    7.配置主题
        

