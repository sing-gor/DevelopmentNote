# run_start 开始运行
地址是`127.0.0.1:9090/start`或`127.0.0.1:10000/start`。取决于你所运行的环境。  
# run_start.py
```python
# 从FastApi中导入 路由方法, 表单方法
from fastapi import APIRouter, Form
# 从apps 的模型中的run_start_item文件中导入 RunStart的响应体
from apps.models.run_start_item import RunStartResponseItem

# 实例化APIRouter() 
# 注意这一步很重要，是为了让main.py中的app能正确导入在下面的路径装饰器
router = APIRouter()


# 该装饰器是将我们的run_start函数绑定到 /start 的post方法中
# response_model是指定我们返回到形式是RunStartResponseItem到结构
# 虽然不加上也行，但是我们在http://localhost:<port>/docs#/default/run_start_start_post
# 页面上将看不到返回到结构，不利于后期改造和增加与其他工程师到沟通成本
@router.post("/start", response_model=RunStartResponseItem)
async def run_start(comm: str = Form(...)):
    """
    获取表单中到comm字段 
    """
    return {"response": '200', "text": comm}

```