# Index 首页
地址是`127.0.0.1:9090`或`127.0.0.1:10000`。取决于你所运行的环境。  

## index.py

```python
# 从工具包中导入 render方法
from toolkit import render
# 从FastApi中导入 路由方法
from fastapi import APIRouter
# 从starlette的请求保重导入 请求类 并命名为request
from starlette.requests import Request as request

# 实例化APIRouter() 
# 注意这一步很重要，是为了让main.py中的app能正确导入在下面的路径装饰器
router = APIRouter()

# 该装饰器是将我们的index函数绑定到根路径的get方法中
@router.get("/")
async def index(requests: request):
    # 由于官方示例的代码太丑了，所以对其进行了一次封装
    return render(requests, 'index.html')

```

## reder函数

```python
# 从starlette的模板化中导入 Jinja2模板
from starlette.templating import Jinja2Templates

# 指定模板的路径
templates = Jinja2Templates(directory="templates")

# 渲染函数
def render(request, template_file, _context=None):
    """
    接收三个参数：
    分别为，请求，模板文件路径，上下文
    
    注意： 上下文中的字典，里面不能使用request这个键名
        会被覆盖掉
    """
    # 如果上下文不为 字典类型 则生成一个 空字典
    context = _context if isinstance(_context, dict) else {}

    context['request'] = request
    
    return templates.TemplateResponse(template_file, context)

```