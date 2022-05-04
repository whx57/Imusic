# Imusic
Base on andriod studio + java ，design a music player including palying ，searching and downloading function。
# **Imusic -Android应用开发实践**  

## 1界面介绍

登录 界面         注册界面（popupwindows实现） 

  ![img](F:\app\Typora\pic\clip_image002.jpg)  ![img](F:\app\Typora\pic\clip_image004.jpg)  

本地音乐列表       音乐搜索页面

  ![img](F:\app\Typora\pic\clip_image002-1651677201836.jpg)   ![img](F:\app\Typora\pic\clip_image004-1651677201836.jpg)  

音乐播放页面        下载页面

   ![img](F:\app\Typora\pic\clip_image002-1651677208178.jpg)  ![img](F:\app\Typora\pic\clip_image004-1651677208178.jpg)  

跳转逻辑流程如下：   

 用户进入app，需要先点击登录页面左下角的注册按钮进入到注册页面，在用户注册完毕后，输入账号和密码即可以登录到app主页面即本地音乐列表页面  在主页面  1用户可以在本地音乐页面点击按钮即可搜索本地所有的音乐  2用户点击音乐列表的item即可进入播放音乐页面  3用户点击搜索按钮，即可进入搜索页面      在搜索页面用户输入想要下载的歌曲即可获取到网易云提供的所有相关音乐的资源，点击即可进入下载弹框。     

## 2运行逻辑

  **一、用户使用流程（介绍程序用户的操作使用流程）**     **用**户点击app进入登录页面，在登录页面点击账号注册，弹出账号注册弹窗，在注册完毕后输入账号密码即可完成登录     ![image-20220504231424024](F:\app\Typora\pic\image-20220504231424024.png)

在本地音乐列表界面用户可以点击最上面的扫描按钮来扫描本地音乐。用户可以通过下方的切换按钮，切换搜索页面和本地音乐列表页面。用户进入搜索页面可以搜索喜欢的歌曲，搜索页面会返回对应的歌曲列表。    ![image-20220504231435844](F:\app\Typora\pic\image-20220504231435844.png)         

用户点击喜欢的音乐就可以进行下载，并弹出下载框告知进度，在下载完成后，可以返回本地音乐页面点击搜索本地歌曲，更新歌曲列表，然后点击刚才下载的歌曲进行，播放。在播放页面用户可以通过按钮和进度条控制歌曲切换和进度跳转。 

![image-20220504231448621](F:\app\Typora\pic\image-20220504231448621.png)

## 3具体方法的实现

  账号密码信息处理     先实现sharedpreference的基本查询和设置方法  ![img](F:\app\Typora\pic\clip_image002.gif)     

​      用户输入证号和密码首先先进行判断输入的内容是否合法，然后对证号进行查询，证号是否存在，再对密码进行查询并与用户输入的密码进行匹配。  ![img](F:\app\Typora\pic\clip_image004.gif)                                                        本地音乐信息处理     先构建song类来实现音乐详细的存储 

 ![img](F:\app\Typora\pic\clip_image006.gif)  

在实现本地音乐扫描方法把音乐添加到song类的list中  ![img](F:\app\Typora\pic\clip_image008.gif) 

 在页面加载时使用getData方法为当前页面的列表的adpter里添加数据，即可把音乐信息显示在当前界面     ![img](F:\app\Typora\pic\clip_image010.gif)     

下载音乐信息处理     先实现url重定向的方法

  ![img](F:\app\Typora\pic\clip_image012.gif)                   

​    再实现json解析方法     用于解析api提供的json数据转化为song类的数据格式  **![img](F:\app\Typora\pic\clip_image014.gif)**     

实现一个getData方法来加载获取到的song类数据并加载到搜索界面的列表当中  ![img](F:\app\Typora\pic\clip_image016.gif)     

实现音乐下载方法     将重定向的url进行链接，然后获取数据，并通过prograssdialog来实时显示下载进度     **![img](F:\app\Typora\pic\clip_image018.gif)**     

## 4存在的问题

​         **一、程序运行、调试过程遇到的问题及解决方法**     因为我是开发完之后才开始写这个报告的，所以过程中很多的bug都没有截图，但是很多bug现在依然印象深刻，列举如下：   

  1.在实现自动切换下一首歌时，我想的是在播放页面初始化的时候开一个线程来进行对播放进度的监控，当播放进度和总长度相同时就切换下一个歌曲，但是这里的问题是我新开的线程不能对初始化页面的view进行操作，这里的解决方法是通过@override继承初始化页面，并在timer中再继承这个新开的thread来实现页面更改       

 2.实现网络资源url的connecttion后始终获取不到资源，但是我用url在chorme浏览器里却能获取到，这个问题困扰了我一周，直到后来我自己在写api时，方向我输入的url和我最终浏览器访问的URL不一致，我才方向网易云的音乐接口获取的资源还需要重定向才来得到真正的资源地址，chorme浏览器自动都已经重定向了，而自己实现则需要再写个方法来获取重定向的地址来得到音乐地址。     

3.在实现页面播放时，一般的音乐app都是在下方同步启动一个播放view，然后这个view可以在播放时选择歌曲，但是当我去实现时发现特别麻烦，如果在播放页面退出时不销毁音乐播放服务后续播放又不好管理，我当时找了很久没有在网上找到音乐播放栏实现相应的教程，所以就存在一个问题是不能在边在播放列表里切歌边播放。于是当时我和老师交流的解决方法时使用sharedprefrence对离开时的播放进度进行存储，当再次点这首歌时加载之前的存储进度，当然这样只是对这个功能不能实现的一个弥补吧。     ![img](F:\app\Typora\pic\clip_image002-1651677401756.gif)    

 4.还有就是最开始时学习使用scrollview时，我把子group生成写成一个方法来 点击button来触发生成 但是在layout添加group时就闪退了，当时解决这个办法是我尝试在scrollview添加lineout布局，再在线性布局里添加其他东西，最后成功显示了，发现是因为scrollview只能有一个child，当然这个问题是最开始学习基础知识不扎实的缘故。    

 5. 【Can't create handler inside thread Thread[Thread-4,5,main] that  has not call】当我尝试在下载进度的过程中来进行对下载进度条的更新，发现报错，这是一个典型的不能在子线程做更新ui，对搜索列表的进度对话框进行修改时报错，我的解决办法是在点击搜索结果列表时把Prograssdialogo进行创建并显示，然后在下载的线程里只进行更新和关闭操作，这样就不会再出现报错。     6【ditable android.widget.EditText.getText()' on a null object  reference】  当我尝试在登录界面的注册弹窗popupwindows里尝试获取输入框内容报这个错，后来发现是findviewbyid不能用原来的方法直接寻找，需要先找到layout，解决代码如下：![img](F:\app\Typora\pic\clip_image004-1651677401756.gif)  

## 4源码

```
https://github.com/371606879/Imusic/
```

