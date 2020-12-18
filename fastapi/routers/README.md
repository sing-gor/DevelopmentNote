# 如何解析程序

我的个人经验是从程序的**Routers**开始阅读。   
简单来说从路由开始，不管你是后端还是前端工程师。  
我们都可以从路由表中找到每个路径所映射的方法和组件。  
不过在这个程序中，没有路由表。  
因为FastApi推荐使用的是装饰器的方式，如下：
```python
from toolkit import render
from fastapi import APIRouter
from starlette.requests import Request as request
router = APIRouter()


@router.get("/")
async def index(requests: request):

    return render(requests, 'index.html')
```

然后在`main.py`中用如下的方式进行涵盖  
```python
from routers import index,run_start,webhook,stop
app = FastAPI()

# 路由表
app.include_router(index.router)
```

接下来，各位可以通过左边的链接进行跳转来阅读该项目工作流程。  
如果想要感受项目可以通过`127.0.0.1:9090/docs`或`127.0.0.1:10000/docs`来通过文档来直接测试程序。
