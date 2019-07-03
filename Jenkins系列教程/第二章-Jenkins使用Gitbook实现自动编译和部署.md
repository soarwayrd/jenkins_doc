### Jenkins使用GitBook实现自动编译和部署
>**GitBook**是一个基于 Node.js 的命令行工具，可使用 Github/Git 和 Markdown 来制作精美的电子书。
>本教程将展示如何自动拉取 Github 上的 Markdown 文件，利用 GitBook 命令行将其编译成静态网页，并部署到指定目录。

+ #### 前期准备
   1、确保Jenkins服务器有安装**node.js**，若未安装请自行下载安装。</br>
   2、确保Jenkins服务器有安装**gitbook-cli命令行工具**，若未安装使用命令 `npm install gitbook-cli -g` 进行安装。</br>
   3、gitbook-cli命令行工具安装完成之后需要将**Jenkins服务的登录帐号**修改为安装命令行工具时登录的系统用户，如果不设置会出现'gitbook' 不是内部或外部命令，也不是可运行的程序的错误。
   </br>
   >如何修改Jenkins服务的登录账号
   >+ 找到Jenkins服务，右键属性，选择登录选项卡，勾选此账号，输入用户名密码，点击确定或应用，最后重启服务即可。
   
   4、为了Jenkins能够**自动获取**新的内容，那么**GitHub仓储在有新的提交时需要及时通知Jenkins**。
    >如何实现GitHub仓储有新的提交时及时通知Jenkins
	>+ 此步骤只能GitHub管理员才能操作，如果你不是管理员请联系管理员。
    >+ 登录GitHub，进入本次构建要用到的工程。
	>+ 在工程主页面点击右上角的 `Settings`，再点击左侧 `Webhooks`，然后点击 `Add webhook`。
	>+ 在 `Payload URL` 位置填入webhook地址**【Jenkins服务器IP:端口/github-webhook】**，必须是外网能访问的地址，再点击底部的 `Add webhook`按钮即可。
   
   5、如果需要将构建结果通过钉钉进行及时通知的话，还需要在Jenkins->系统管理->插件管理中安装**Dingding[钉钉]插件**。
+ #### 新建Jenkins任务
   1、**General**，填写任务描述。
   </br>
   2、**源码管理**，勾选Git，在Repository URL处填写GitHub工程的地址，分支（Branch）根据实际情况填写。
   </br>
   3、**构建触发器**，勾选GitHub hook trigger for GITScm polling，在GitHub上有新提交时触发任务自动构建。
   </br>
   4、**构建**，增加构建步骤，执行Windows 批处理命令，输入命令行 `gitbook install && gitbook build`进行编译，若想要将编译好的文件部署到指定目录可以再增加一个执行Windows 批处理命令的步骤，输入命令 `xcopy _book "部署文件夹物理路径" /e /y`将`_book`下的所有文件复制到部署目录下。
   </br>
   5、**构建后操作**，如果想通过钉钉实时通知构建结果，那么可以增加构建后操作步骤钉钉通知器配置，填写钉钉access token，以及勾选需要通知的状态即可。
   </br></br>
   >如何获取钉钉access token
   >+ 首先我们需要一个钉钉群，然后创建一个自定义的群机器人，如果你不是管理员是无法创建群机器人的，可以自己创建一个群来测试。
   >+ 群机器人创建好后找到webhook，其中webhook的access_token参数的值就是我们要的钉钉access token。

### End