---
title: Python解析JSON数据
tags: [Python, JSON]
---

JSON，全称是 JavaScript Object Notation，是我们常用的一种存储和交换文本信息的语法，和 XML 类似。相对于 XML，JSON 具有更小、更快和更容易解析的优势。

既然 JSON 有这么多优点，那么作为编程语言界的瑞士军刀，Python 语言对 JSON 的支持也肯定是相当完备。今天，笔者就来总结一下使用 Python 操作 JSON 数据的基本方法。



## JSON 的基本语法

JSON 的语法比较简单，主要遵循如下规则：

1. 数据以`键:值`（`key:value`）的形式保存。
2. `键/key`必须是字符串。
3. `值`可以是字符串、数字（整数、浮点数）、布尔值（`true`或`false`）、对象、数组或者 null。
4. 数据之间使用逗号（`,`）分隔。

示例：

```JSON
{
    "name": "唐僧",
    "age": 18,
    "height": 180.0,
    "male": true,
    "salary": null
}
```

5. JSON 对象是指由大括号（`{}`）修饰的数据，对象中可以包含多个`键/值`对。例如：

```JSON
{
    "students": {
        "name": "孙悟空",
        "age": 500
    }
}
```

6. JSON 数组是指由中括号（`[]`）修饰的一个或多个数据。数组中可以包含任何合法的 JSON 数据类型，即字符串、数字（整数、浮点数）、布尔值（`true`或`false`）、对象、数组或者 null。例如：

```json
{
	"friends": ["如来佛祖", "观音菩萨"],
    "other_students": [
        { "name": "猪八戒", "age": 300 },
        { "name": "沙和尚", "age": 200 }
    ]    
}
```



## Python 库的基本方法

Python 使用名为 json 的库来操作 JSON 数据。该库包含以下常用方法：

```python
import json

'''
json.dumps(): 将 Python 数据对象编码成 JSON 字符串。
json.loads(): 将 JSON 字符串解码成 Python 数据对象。
json.dump(): 将 Python 数据对象编码成 JSON 字符串后存储到文件中。
json.load(): 读取 JSON 文件中的数据并转换为 Python 数据对象。
'''

```

### json.dumps()

**模拟场景**：假设我们有一组 Python 数据，需要将其转换为 JSON 格式的字符串，用于网络传输，那么就需要使用 `json.dumps()`方法。

示例代码如下：

```python
# 导入 json 库
import json

# 要传输的 Python 数据对象
python_data = {
    "name": "唐僧",
    "age": 18,
    "height": 180.0,
    "male": True,
    "students": {
        "name": "孙悟空",
        "age": 500
    },
    "friends": ["如来佛祖", "观音菩萨"],
    "other_students": [
        {"name": "猪八戒", "age": 300},
        {"name": "沙和尚", "age": 200}
    ],
    "salary": None
}

# 转换为 JSON 格式字符串，并输出。
# 添加 ensure_ascii=False 是为了将中文字符使用 utf-8 编码。
json_str = json.dumps(python_data, ensure_ascii=False)
print("数据类型：", type(json_str))
print("数据内容：", json_str)
```

程序运行结果如下：

```text
数据类型： <class 'str'>
数据内容： {"name": "唐僧", "age": 18, "height": 180.0, "male": true, "students": {"name": "孙悟空", "age": 500}, "friends": ["如来佛祖", "观音菩萨"], "other_students": [{"name": "猪八戒", "age": 300}, {"name": "沙和尚", "age": 200}], "salary": null}
```



### json.loads()

**模拟场景**：假设我们收到了一组 JSON 格式的字符串，需要将其转换成 Python 数据对象之后进行数据分析，那么就需要使用`json.loads()`。

示例代码如下：

```python
# 导入 json 库
import json

# JSON 格式字符串
json_str = '{"name": "唐僧", "age": 18, "height": 180.0, "male": true }'

# JSON 字符串转化为 Python 数据对象
python_data = json.loads(json_str)

# 输出 Python 数据对象的类型
print("数据类型：", type(python_data))

# 输出 Python 数据对象的值
print(python_data)
```

程序运行结果：

```
数据类型： <class 'dict'>
{'name': '唐僧', 'age': 18, 'height': 180.0, 'male': True}
```



### json.dump()

**模拟场景**：假设我们有一组 Python 数据，需要将其转换为 JSON 格式的字符串并直接保存到指定的文件中，那么就可以使用`json.dump()`方法。

示例代码如下：

```python
# 导入 json 库
import json

# 要保存的 Python 数据对象
python_data = {
    "name": "唐僧",
    "age": 18,
    "height": 180.0,
    "male": True,
    "students": {
        "name": "孙悟空",
        "age": 500
    },
    "friends": ["如来佛祖", "观音菩萨"],
    "other_students": [
        {"name": "猪八戒", "age": 300},
        {"name": "沙和尚", "age": 200}
    ],
    "salary": None
}

# 以写入方式打开文件
with open("new_file.json", mode="wt", encoding="utf-8") as f_wr:
    # 将 Python 数据对象转换为 JSON 格式并保存到文件中。
    # 如果不配置 ensure_ascii=False，默认会使用 ASCII 编码中文字符。
    json.dump(python_data, f_wr, ensure_ascii=False)
```

代码运行后，新创建的文件内容如下：

```json
{
    "name": "唐僧",
    "age": 18,
    "height": 180.0,
    "male": true,
    "students": {
        "name": "孙悟空",
        "age": 500
    },
    "friends": [
        "如来佛祖",
        "观音菩萨"
    ],
    "other_students": [
        {
            "name": "猪八戒",
            "age": 300
        },
        {
            "name": "沙和尚",
            "age": 200
        }
    ],
    "salary": null
}
```



### json.load()

模拟场景：假如我们需要读取 JSON 文件的内容，转换为 Python 数据对象之后进行数据分析，那么就可以使用`json.load()`方法。

示例代码：

```python
# 导入 json 库
import json

# 以读取方式打开文件，选择 utf-8 解码
with open("example.json", mode="rt", encoding="utf-8") as f_rd:
    # 获取 JSON 文件内容
    python_data = json.load(f_rd)

    # 输出数据对象的类型
    print("数据类型：", type(python_data))

    # 输出数据对象的内容
    print("数据内容：", python_data)
```

运行结果：

```
数据类型： <class 'dict'>
数据内容： {'name': '唐僧', 'age': 18, 'height': 180.0, 'male': True, 'students': {'name': '孙悟空', 'age': 500}, 'friends': ['如来佛祖', '观音菩萨'], 'other_students': [{'name': '猪八戒', 'age': 300}, {'name': '沙和尚', 'age': 200}], 'salary': None}

```

