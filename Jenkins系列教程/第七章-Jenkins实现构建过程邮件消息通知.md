### Jenkins实现构建过程邮件消息通知
+ #### 实现目标
在上一章教程中已经教大家如何通过钉钉实现消息实时通知，接下来教大家另外一种通知方式：`邮件`。邮件通知相较于钉钉有更丰富的通知机制，值得大家好好看看，以下均以QQ邮箱为例。

+ #### 前期准备
   1、**准备用来发送消息的邮箱**，打开QQ邮箱->设置->账户->开启POP3/SMTP服务，开启之后会得到一串授权码，记住等会要用，得到如下结果：  
   > 特别说明：授权码只有在QQ修改密码之后才会失效，失效后需要重新授权。

   ![avatar](/Images/qqemail.png)

   2、**安装配置插件**，系统管理->插件管理找到 `Email Extension Plugin` 插件，下载并安装。安装完成之后再打开系统管理->系统设置，找到 `Extended E-mail Notification` 选项进行如下配置：
   ![avatar](/Images/emailsetting.png)

   接上图

   ![avatar](/Images/emailsetting2.png)
    > 特别说明：管理员邮箱的配置也在系统管理->系统设置->Jenkins Location->系统管理员地址

   附上默认的邮件内容：
   ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">    
        <title>${ENV, var="JOB_NAME"}-第${BUILD_NUMBER}次构建日志</title>    
    </head>    
    <body leftmargin="8" marginwidth="0" topmargin="8" marginheight="4"    
    offset="0">    
        <table width="95%" cellpadding="0" cellspacing="0"  style="font-size: 11pt; font-family: Tahoma, Arial, Helvetica, sans-serif">    
            <tr>    
                本邮件由系统自动发出，无需回复！<br/>            
                各位同事，大家好，以下为${PROJECT_NAME }项目构建信息</br> 
                <td><font color="#CC0000">构建结果 - ${BUILD_STATUS}</font></td>   
            </tr>    
            <tr>    
                <td><br />    
                <b><font color="#0B610B">构建信息</font></b>    
                <hr size="2" width="100%" align="center" /></td>    
            </tr>    
            <tr>    
                <td>    
                    <ul>    
                        <li>项目名称 ： ${PROJECT_NAME}</li>    
                        <li>构建编号 ： 第${BUILD_NUMBER}次构建</li>    
                        <li>触发原因： ${CAUSE}</li>    
                        <li>构建状态： ${BUILD_STATUS}</li>    
                        <li>构建日志： <a href="${BUILD_URL}console">${BUILD_URL}console</a></li>    
                        <li>构建  Url ： <a href="${BUILD_URL}">${BUILD_URL}</a></li>    
                        <li>工作目录 ： <a href="${PROJECT_URL}ws">${PROJECT_URL}ws</a></li>    
                        <li>项目  Url ： <a href="${PROJECT_URL}">${PROJECT_URL}</a></li>    
                    </ul>    
                    <h4><font color="#0B610B">失败用例</font></h4>
                    <hr size="2" width="100%" />$FAILED_TESTS<br/>
                    <h4><font color="#0B610B">最近提交(#$SVN_REVISION)</font></h4>
                    <hr size="2" width="100%" />
                    <ul>${CHANGES_SINCE_LAST_SUCCESS, reverse=true, format="%c", changesFormat="<li>%d [%a] %m</li>"}
                    </ul>
                    详细提交: <a href="${PROJECT_URL}changes">${PROJECT_URL}changes</a><br/>
                </td>    
            </tr>    
        </table>
    </body>
    </html>
  ```

+ #### 新建Jenkins任务
   1、**General**，填写任务描述。  
   2、**构建**，可以随便填点什么，主要是实现邮件推送，这步没那么重要，但最少要能保证构建通过哦。
   3、**构建后操作**，添加构建后操作 `Editable Email Notification`，详细配置参考下图：

   ![avatar](/Images/editableemail.png)
   > 各项配置说明

   > `Disable Extended Email Publisher`：勾选后，邮件就不发送，看自己的情况喽，如果你想调试某些东西，又不想发邮件出去就可以勾选这个。

   > `Project Recipient List`：收件人地址；多个收件人邮件地址用英文逗号进行分割；想使用全局默认配置的话，可以使用$DEFAULT_RECIPIENTS。

   > `Project Reply-To List`：允许回复人的地址；想使用系统设置中的默认值的话，可以使用$DEFAULT_REPLYTO;  

   > `Content Type`：邮件文档的类型，可以设置HTML等格式；

   > `Default Subject`：默认主题，也就是邮件标题；同理可以使用$DEFAULT_SUBJECT。

   > `Default Content`：默认邮件内容；这里是关键；我这里使用的是模板${SCRIPT, template="groovy-html.template"}；后面会讲；当然不想使用模板的话，可以通过使用jenkins自身提供的变量来自己定义；

   > `Attach Build Log`：发送的邮件是否包含日志

    Advanced Settings：
    
    ![avatar](/Images/advancedsetting.png)

   3、**构建结果**，以上配置都完成后，点击构建测试看是否能收到邮件，收到的邮件效果如下：

   ![avatar](/Images/emailresult.png)
+ #### 扩展说明
在构建时如果出现构建失败或者是构建成功但未收到邮件的情况，请认真检查各项配置是否正确，特别是系统设置，我遇到的坑就是浏览器把其他密码覆盖掉了我的授权码，一不小心保存后死活收不到邮件，要特别注意。
### End