# webhook
地址是`127.0.0.1:9090/webhook`或`127.0.0.1:10000/webhook`。取决于你所运行的环境。  
# run_start.py
~~~python
# 从FastApi中导入 路由方法, Http额外处理
from fastapi import APIRouter, HTTPException
# 从函数工具中导入 累计方法
from functools import reduce
# 从工具包中导入 发送信息给机器人方法
from toolkit import send_message_to_robot
# 从 应用的模型中导入 webhook_item 模型
from apps.models import webhook_item
# 从应用的模型中webhook_item 导入 提交处理 提交格式化
from apps.models.webhook_item import commit_handler, commit_format

# 实例化APIRouter()
# 注意这一步很重要，是为了让main.py中的app能正确导入在下面的路径装饰器
router = APIRouter()

# 根据 gitlab webhook接口 初步定义的 字典
# 详情请查阅下方的地址
# https://docs.gitlab.com/ee/user/project/integrations/webhooks.html#deployment-events
items = {
    "push": "提交",
    "merge_request": "合并分支",
    "tag_push": "提交标签",
    "note": "标记",
    "pipeline": "流向",
    "build": "打包",
    "deployment": "部署",
    "feature_flag": "标记功能",
    "release": "发布"
}


# 该装饰器是将我们的webhook函数绑定到 /webhook 的post方法中
@router.post("/webhook")
async def webhook(item: webhook_item.WebHookRequstItem):
    action = item.object_kind
    """
    Web Hook

    根据接收到的commit 信息 对其进行格式化 然后发送到企业微信的消息机器人中
    """
    # 如果 item的 对象类型 不在 字典中 返回404 错误
    if action not in items:
        raise HTTPException(status_code=404, detail="object_kind 匹配失败，请联系管理员")
    # 将接收到的 commits 内容进行格式化
    commits = reduce(commit_handler, map(commit_format, item.commits))

    # 消息模版
    text = f"""
# {item.user_name} {items[action]}（{item.repository.name}）

WHAT'S NEW:

```
{commits}
```
    """
    # 发送到企业微信的消息机器人中
    send_message_to_robot(text)
    return {"response": '200', "text": text}
~~~
