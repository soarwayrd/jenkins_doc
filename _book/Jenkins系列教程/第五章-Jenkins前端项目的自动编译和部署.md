### Jenkins前端项目的自动编译和部署

+ #### 项目背景
	+ 项目：微网站移动端
	+ 开发工具：Visual Studio Code
	+ 开发语言：Angular，使用Ionic Framework作为前端用户界面
	+ 源码控制管理软件：Git
	+ 源码审查软件：Gerrit
+ #### 实现目标
实现前端项目的编译发布，并部署到指定目录，最后通过钉钉通知构建结果，**若要实现Git提交源码后任务自动构建，可将第三章与本章教程结合使用**。

+ #### 前期准备
   1、**安装Ionic**，由于编译需要用到Ionic，如Jenkins服务器未安装Ionic,请使用命令行 `npm install -g ionic`进行安装。  
   2、**钉钉通知**，如果需要将构建结果通过钉钉进行及时通知的话，还需要在Jenkins->系统管理->插件管理中安装**Dingding[钉钉]插件**。
+ #### 新建Jenkins任务
   1、**General**，填写任务描述。  
   2、**源码管理**，勾选Git，在Repository URL处填写源码仓储的地址，如：ssh://sw.gerrit/smart_micro_site_mobile ；分支（Branch）根据实际情况填写。  
   3、**构建**，增加 `Windows批处理命令`构建，输入命令行 `npm install && ionic build --prod`用来编译项目，再增加一个`Windows批处理命令`构建，输入命令行 `xcopy www "部署文件夹物理路径" /e /y`将发布文件复制到指定部署目录。
   4、**构建后操作**，如果想通过钉钉实时通知构建结果，那么可以增加构建后操作步骤钉钉通知器配置，填写钉钉access token，以及勾选需要通知的状态即可。
   >如何获取钉钉access token
   >+ 首先我们需要一个钉钉群，然后创建一个自定义的群机器人，如果你不是管理员是无法创建群机器人的，可以自己创建一个群来测试。
   >+ 群机器人创建好后找到webhook，其中webhook的access_token参数的值就是我们要的钉钉access token。

### End