# stop 停止服务
地址是`127.0.0.1:9090/__exit`或`127.0.0.1:10000/__exit`。取决于你所运行的环境。  
## stop.py

```python
# 从FastApi中导入 路由方法
from fastapi import APIRouter

# 实例化APIRouter() 
# 注意这一步很重要，是为了让main.py中的app能正确导入在下面的路径装饰器
router = APIRouter()

# 该装饰器是将我们的__exit函数绑定到 /__exit 的get方法中
@router.get("/__exit")
async def __exit():
    # 导入 os 和信号
    import os,signal
    """
    ！！！！！！注意该路径会导致停止项目！！！！！！！！

    获取该服务的进程id
    杀死进程
    """
    id = os.getpid()
    os.kill(int(id), signal.SIGKILL)

```