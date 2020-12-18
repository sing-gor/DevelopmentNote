# 启动项目

流程如下：

- 创建并激活虚拟环境
- 尝试执行`python main.py`
- 根据缺少的依赖执行`pip install <xxxxx>`  
- 直至项目启动


项目启动后，根据终端机提示。  
可以知道项目运行在**9090**端口上。  

## 分析项目

但凡是Web项目，务必用以下的流程进行分析（个人经验）。  
- 找到该项目的**路由表**，通常名为**router**或**url**。  
- 根据路径所对应的处理方法，找到该处理方法。
- 使用浏览器或PostMan工具对地址进行测试和观察
- 在该对应的处理方法上写上个人理解和注释

### 路由表

```python
app = Bottle()
app.route('/webhook/', method='POST')(webhook)
app.route('/', method='GET')(hollo)
app.route('/__exit', method=['GET', 'HEAD'])(__exit)
app.route('/assets/<filepath:path>', method='GET')(server_static)

app.route('/start/', method='POST')(run_start)
```

由上可得出，该项目有以下的路径方法。  
```bash
# hollo
# http://localhost:9090/

# webhook
# http://localhost:9090/webhook/

# assets
# http://localhost:9090/assets/

# __exit
# http://localhost:9090/__exit

# start
# http://localhost:9090/start
```

解析如下：

#### hollo
```python
def hollo():
    # MPV, Based on bootstrap and jquery ad front-end
    return """
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
  </head>
  <body>
  内容省略……
  </body>
</html>
"""
```
该方法是返回一个html文件，不过**hollo**这个命名不是很严谨，后续应该改为**index、home、page、hello**之类的用词会更好。   
且不应该将这么长html内容写在python代码中。  
后续要抽离到一个html文件中会更为适合。

#### webhook
~~~python
def webhook():
    # 获取请求中的json数据
    json_data = request.json
    # 生成一个空字符串
    msg = ''
    # 如果json中的 object_kind 不为 merge_request（请求合并分支）
    # pass 不处理，
    if json_data['object_kind'] == 'merge_request':
        pass
    # 如果 json中的 object_kind 为 push（推送）
    elif json_data['object_kind'] == 'push':
        # 这列表推导式写的也太丑了把
        # 感觉可以用reduce函数来解决
        # 注： reduce和列表推导式的效能几乎是相等的
        comments_message = "\n".join(
            [item['message'] + "\n( " + item['url'] + " )\n" for item in json_data['commits']])
        # 消息文本替换模版    
        # 注意： 其中的两个反斜线是为了在markdown中不出现代码情况所添加的
        # 正常是没有的
        msg_tpl = """# %s 提交（%s）

WHAT'S NEW:

```
%s
```"""
        # 生成消息
        msg = msg_tpl % (
            json_data['user_name'], json_data['repository']['name'], comments_message)
    # 打印消息
    print(msg)
    # 发送消息到企业微信机器人中
    send_message_to_robot(msg)
    # 返回json数据作为响应
    return {"response": msg}
```
经过测试，我们应该提交的json数据如下：  
```json
{
    "object_kind": "push",
    "commits": [
        {"message": "修复了位于view中的一个bug", "url": "https://github.com/sing-gor/BigTeaRice"},
        {"message": "落魄前端，在线炒粉", "url": "https://github.com/sing-gor/BigTeaRice"},
        {"message": "三元一份，十元三份", "url": "https://github.com/sing-gor/BigTeaRice"},
        {"message": "谁在代码里下的毒？？？", "url": "https://github.com/sing-gor/BigTeaRice"}
    ],
    "user_name": "sing-gor",
    "repository": {
        "name": "BigTeaRice"
    }
}
~~~
而返回的信息如下：
```json
{
    "response": "# sing-gor 提交（BigTeaRice）\n\nWHAT'S NEW:\n\n```\n修复了位于view中的一个bug\n( https://github.com/sing-gor/BigTeaRice )\n\n落魄前端，在线炒粉\n( https://github.com/sing-gor/BigTeaRice )\n\n三元一份，十元三份\n( https://github.com/sing-gor/BigTeaRice )\n\n谁在代码里下的毒？？？\n( https://github.com/sing-gor/BigTeaRice )\n\n```"
}
```

该方法的处理逻辑极其混乱，后续应该将`merge_request`和`push`拆离出来，放到url路径中进行处理。  
消息是否为空的判断处理没有做，而是直接发送**空消息**到企业微信的机器人中。  
总体来说，后续需要重写。   


#### assets
```python
#  静态文件服务
def server_static(filepath):
    return static_file(filepath, root='./')
```

整个项目都没用到该路径。建议**注释**掉该方法和路径。  

#### __exit
```python
def __exit():
    global server
    server.stop()
```
在浏览器中打开`http://localhost:9090/__exit`后会终止项目运行。  
但是没必要这样终止项目运行，可以通过python中的os库。  
先获取**进程号**，然后根据进程号来**杀掉**进程。  

#### run_start
```python
def run_start():
    # 从请求的 表单中获取 comm字段
    comm = request.forms.get('comm')
    # 响应一段json代码

    return {"response": '200', "text": comm}
```

##### Input
`curl --location --request POST 'http://127.0.0.1:9090/start/' \
--form 'comm=下达指令'`

##### Output
```json
{
    "response": "200",
    "text": "下达指令"
}
```

这个form表单处理形式感觉是结合前端ajax来配合的。   

### 环境变量部分
由于我决定使用Docker进行部署，所以没必要进行分析。  
采取`os.environ.get('ROBOT_URL')`来获取环境变量
