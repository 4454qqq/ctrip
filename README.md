# 携程训练营大作业 -- Trail-Tales游记平台
## 项目介绍
项目分为游记发布、呈现的用户系统和内容合规检查的审核管理系统。其中对客用户使用的游记发布系统为一个移动端项目，可以在手机端发布游记以及查看、分享所有已成功发布的游记；审核管理系统为一个 PC 站点，不同角色可以对用户发布的游记做上线前的审核检查、删除等操作。<br><br>
仓库中的项目代码分为三个子模块，其中**移动端对应`trail-tales-app`**，**PC审核端对应`PC-management-system`**，**后端服务器及数据库搭建对应`back-end`**
## 环境配置
- ### 移动端<br>
  技术栈：React Native、Nodejs<br><br>
  1、 app使用expo沙盒环境搭建，初始化命令如下，在本项目中只需使用`npm install`命令安装依赖即可使用<br>
  ```
  npx create-expo-app trail-tales-app
  ```
  2、 app运行需要结合**Expo Go**软件来查看界面，可以选择真机以及模拟器两种方式打开，具体安装步骤可到官网上查看，下载时要与使用的expo沙盒环境的版本相对应，项目测试时使用的是Android SDK 53版本<br>
  ![image](pictures/1.png)
- ### PC审核端<br>
  技术栈：React Native、Nodejs<br><br>
  1、 审核端初始化命令如下，在本项目中只需使用`npm install`命令安装依赖即可使用<br>
  ```
  npx create-react-app PC-management-system
  ```
  2、 审核端登陆界面不提供注册功能，需要在后端运行`initManger.js`来初始化审核账号，建议初始化时创建超级管理员账号，在审核页面中会根据当前登录账号的权限提供不同的新建账号功能
- ### 后端<br>
  技术栈：Nodejs<br><br>
  后端服务器使用Nodejs搭建，数据库使用Mongodb数据库，项目运行时将其部署在本地，数据库的使用可以到官网上查看，前期需要在后端向数据库写入审核端所需的账号信息
  ```
  node initManger.js   // 写入管理员信息
  node server.js      // 运行后端服务器
  ```
## 页面设计
### app页面设计

- 首页（游记列表页）：该页面不验证用户的登录状态，通过审核的游记会以瀑布流的形式展示对应的游记卡片，点击卡片会进入到游记详情页，可以通过上方的搜索栏或主题挑选喜欢的游记展示<br>
![image](pictures/2.png)
![image](pictures/3.png)
![image](pictures/4.png)


- 游记详情页：该页面会显示游记的详细内容，包含发布游记的作者信息及游记内容，游记的视频和图片可以左右滑动显示，如果有视频资源则放在首位展示，点击视频可以全屏播放，点击图片可以放大显示，同时点击作者头像可以跳转至其主页，如果是本人发布的则跳转至个人主页<br>
![image](pictures/5.png)
![image](pictures/6.png)
![image](pictures/7.png)
![image](pictures/8.png)
![image](pictures/9.png)


- 我的游记页：该页面会先验证登录状态，未登录时会给出跳转登录界面的按钮，登录后显示用户的个人信息以及已发布的游记，游记位置会显示当前状态，可以对游记进行删除的操作，已通过审核的游记点击卡片会跳转至详情页，未审核和未通过的游记还可以进行编辑的操作，点击编辑直接跳转到游记发布页，并自动填充历史记录，点击退出登录按钮会弹出退出对话窗，此外也可以对用户头像进行修改<br>
![image](pictures/10.png)
![image](pictures/11.png)
![image](pictures/12.png)
![image](pictures/13.png)
![image](pictures/14.png)


- 游记发布页：用户登录后可以通过主页面的发布入口进入发布页，该页面可以通过相册上传图片及视频，其中图片最多上传六张，并且上传时对其进行了压缩处理，视频最多上传一个，提交时会进行校验<br>
![image](pictures/17.png)
![image](pictures/15.png)


- 登录界面：该页面实现了用户的登录功能，登录时账号重复、账号不存在、密码错误、账号或密码未输入均会弹出错误提示，同时在登录这里我们使用`rateLimit`对其增加了限流的功能，即每一段时间同一ip同一用户名登录失败的次数有限，达到一定失败的次数会提示操作频繁，并将登录按钮锁定，防止暴力破解密码<br>
![image](pictures/18.png)
![image](pictures/19.png)
![image](pictures/20.png)


- 注册界面：用户可以在这里注册自己的账户，会对密码进行二次校验，若用户已存在会提示，注册功能同样添加了限流功能，同一ip在一定时间段只能注册有限个账号<br>
![image](pictures/21.png)
![image](pictures/22.png)
![image](pictures/23.png)

### PC审核端页面设计


## 功能实现

### 移动端APP
- 数据存储：项目中app及pc端的数据使用mongodb数据库来管理，在后端使用`mongoose`库实现对数据库的操作
  
- 路由：使用expo初始化完毕所提供的路由规则，将页面全部存储在app文件夹中，以index开头的文件为路由入口，页面的路由路径根据其与app文件夹的相对路径来设置，导航功能主要使用`expo-router`来实现，具体内容可到其官方文档查看。

- 首页瀑布流卡片：将每个游记展示所使用的卡片封装为`TravelLogCard`组件，瀑布流的展示使用`WaterfallFlow`组件实现，当游记数量大于设置的基准数量时，使用`onEndReached`属性实现无限下拉刷新功能

- 发布页图片视频上传：使用`ImagePicker.launchImageLibraryAsync()`实现对图片和视频的选取，并用`mediaTypes`对选取的类型进行限定，图片的压缩通过设置`quality`实现，以FormData的格式上传给后端。
  
- 游记详情页图片和视频展示：将展示的内容封装为组件`mediaSlider`，根据图片资源的最大宽高比来等比例缩放全部图片，设置`contain`属性保证显示时不会丢失，使用`FlatList`组件渲染图片，设置`horizontal`属性为`true`实现水平滑动，`pagingEnabled`属性为`true`实现左右滑动时分页展示的效果。

- 登录、注册界面限流功能：在后端使用`rateLimit`设置限流的参数，根据后端是否响应状态码`429`来决定限流是否执行，其中登录使用`ip+username`来进行匹配，不计入登录成功的次数，防止暴力破解密码，注册使用`ip`来匹配，不计入注册失败的次数，防止恶意注册。在前端使用`AsyncStorage`持久化存储后端传来的限流时间`retryAfter`，然后使用计时器动态显示剩余限流时间。

### PC端



