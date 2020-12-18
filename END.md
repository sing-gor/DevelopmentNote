# 项目用途

该项目用途是**发送GIT commit的信息到企业微信机器人中**。  
用于平时开发工作中，及时推送信息给工程师或主管人员。   

虽然GitLab有内置了Slasck推送，但是在中国，人们日常使用微信的频率较多，所以需要开发这个项目。   

## 证明方式

将原来旧项目中的注释，**GitLab 和 webhook**两个关键字，丢到谷歌中，第一个结果就是[GitLab WebHook](https://docs.gitlab.com/ee/user/project/integrations/webhooks.html#overview)  
经比对，和项目中的代码的逻辑相符。

而且`send_message_to_robot`函数的结果是有一个[错误码地址](https://open.work.weixin.qq.com/devtool/query?e=93000)。  
打开后发现是企业微信。  
