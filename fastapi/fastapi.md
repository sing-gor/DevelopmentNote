# 使用FastApi进行项目重写

由原来的bottle写的旧项目可知，我们要改写成fastApi的话，需要以下知识。  
- Form（表单）
- 渲染模板文件（html之类文件的渲染）
- json数据的Serialization（即接收json数据的格式验证及处理）
- 路由的语法
- FastApi 的项目布局

## 阅读FastApi的文档

整个[FastApi文档](https://fastapi.tiangolo.com/)阅读下来大概需要花3个小时，不需要跟着做任何练习。  
在浏览器中新建一个收藏夹或新建一个文本文件，只需要将上面提到**所用到或我个人认为有用的知识点**的网址记录下来。   
由于阅读该文档发现，FastApi结合了[pydantic](https://pydantic-docs.helpmanual.io/)和[typing](https://docs.python.org/3/library/typing.html)需要额外花一小时阅读学习他们  

记录如下：  
- [Install FastApi （安装FastApi）](https://fastapi.tiangolo.com/tutorial/)
- [Run Server 运行一个基本项目](https://fastapi.tiangolo.com/tutorial/first-steps/)
- [Reuqest Body（请求体）](https://fastapi.tiangolo.com/tutorial/body/)
- [Form （表单）](https://fastapi.tiangolo.com/tutorial/request-forms/)
- [路由语法](https://fastapi.tiangolo.com/tutorial/path-params/)
- [结构体字段](https://fastapi.tiangolo.com/tutorial/body-fields/)
- [嵌套结构体](https://fastapi.tiangolo.com/tutorial/body-nested-models/)
- [Handling Errors（错误处理）](https://fastapi.tiangolo.com/tutorial/handling-errors/)
- [模板文件](https://fastapi.tiangolo.com/advanced/templates/)
- [静态文件](https://fastapi.tiangolo.com/tutorial/static-files/)
- [项目结构布局](https://fastapi.tiangolo.com/tutorial/bigger-applications/)
- [使用Docker 进行部署](https://fastapi.tiangolo.com/deployment/docker/)

再花点时间将上面罗列的页面进行深入学习。

## 项目结构的规划
```bash
.
├── __inin__.py
├── apps # 应用的目录
│   ├── __init__.py
│   └── models # 模型目录
│       ├── __init__.py
│       ├── run_start_item.py
│       └── webhook_item.py
├── main.py # 主程序
├── requirements.txt # 项目相依文件列表
├── routers # 路由配置包
│   ├── __init__.py
│   ├── index.py # 首页
│   ├── run_start.py # start页面
│   ├── stop.py # 停止服务
│   └── webhook.py # webhook服务
├── templates # 模板文件目录
│   └── index.html # 首页的模板文件
└── toolkit.py # 工具包

3 directories, 14 files
```

上面的项目结构只是根据旧的bottle的功能所做的项目规划，并不是**FastApi的Boilerplate**。  
如果是大型项目，还要另外写一个config和db层的，并且要将routers目录中的函数方法再拆分一个services层来做SOC(Separation of concerns)。  

