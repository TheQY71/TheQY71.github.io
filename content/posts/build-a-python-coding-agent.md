+++
date = '2026-04-06T15:07:41+08:00'
draft = false
title = '我要成为Agent大师1：Build a python coding agent'
+++

# Build a python coding agent

## reference

https://www.youtube.com/watch?v=YtHdaXuOAks

1. 接受一个编程任务（例如：“我的应用里字符串没有正确拆分，求求帮我修一下 🥺👉👈”）

2. 从一组预定义函数中选择来处理该任务，例如：
   • 扫描目录中的文件
   • 读取文件内容
   • 覆盖写入文件内容
   • 在某个文件上执行 Python 解释器

3. 重复步骤 2，直到任务完成（或者彻底失败——这种情况也是有可能的）


## 初始化项目

### 第一次对话

1. 创建一个空项目

```shell
uv init
```

2. 配置api和模型
我使用的是百炼平台的免费api

创建`.env`
```
DASHSCOPE_MODEL_NAME="qwen3-max-2026-01-23"
DASHSCOPE_API_KEY="填你的api"
```

3. 测试模型

```python
import os
import sys
from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()

def main():
    if len(sys.argv) < 2:
        print("Usage: python script.py <args>")
        exit(1)
    prompt = sys.argv[1]
    model_name = os.environ.get("DASHSCOPE_MODEL_NAME")
    client = OpenAI(
        api_key=os.environ.get("DASHSCOPE_API_KEY"),
        base_url="https://dashscope.aliyuncs.com/compatible-mode/v1",
    )
    completion = client.chat.completions.create(
        model=model_name, messages=[{"role": "user", "content": prompt}]
    )
    print(completion.choices[0].message.content)
    print(f"Prompt tokens:{completion.usage.prompt_tokens}")
    print(f"response tokens:{completion.usage.completion_tokens}")


if __name__ == "__main__":
    main()
```

运行结果：
```python
─ uv run main.py "hello. Who is the tallest man?"
Hello! The tallest man in recorded history was **Robert Wadlow**, also known as the "Alton Giant." He was born on February 22, 1918, in Alton, Illinois, USA, and passed away on July 15, 1940, at the age of 22.

Robert Wadlow reached a height of **8 feet 11.1 inches** (2.72 meters), according to measurements taken shortly before his death. His extraordinary height was due to a condition called **gigantism**, caused by an overproduction of growth hormone from a benign tumor on his pituitary gland.

He remains the tallest person ever verified by medical professionals and is recognized by Guinness World Records as the tallest man in history.
Prompt tokens:16
response tokens:162
```


### `verbose`

增加一个命令参数`--verbose`, 控制输出是否冗长。

如果包含这个参数，除了输出模型响应，还会输出**User prompt**, **Prompt tokens, **response tokens**

```python
import os
import sys
from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()


def main():
    if len(sys.argv) < 2:
        print("Usage: python script.py <args>")
        exit(1)
    verbose_mode = False
    if len(sys.argv) == 3 and sys.argv[2] == "--verbose":
        verbose_mode = True
    prompt = sys.argv[1]
    model_name = os.environ.get("DASHSCOPE_MODEL_NAME")
    client = OpenAI(
        api_key=os.environ.get("DASHSCOPE_API_KEY"),
        base_url="https://dashscope.aliyuncs.com/compatible-mode/v1",
    )
    completion = client.chat.completions.create(
        model=model_name, messages=[{"role": "user", "content": prompt}]
    )
    print(completion.choices[0].message.content)
    if verbose_mode:
        print(f"User prompt:{prompt}")
        print(f"Prompt tokens:{completion.usage.prompt_tokens}")
        print(f"response tokens:{completion.usage.completion_tokens}")


if __name__ == "__main__":
    main()
```

## 计算器

### 实现计算器

#### 介绍

实现一个简单的计算器程序

```python
.
├── main.py
├── pkg
│   ├── __init__.py
│   ├── calculator.py
│   └── render.py
└── test.py

2 directories, 5 files
```



#### calculator

`pkg/calculator.py`

```python
import operator
import re


class Calculator:
    OPERATORS = {
        "+": operator.add,
        "-": operator.sub,
        "*": operator.mul,
        "/": operator.truediv,
    }

    def calculate(self, expression: str) -> float:
        if not expression or not expression.strip():
            raise ValueError("Empty expression")

        pattern = r"^\s*(-?\d+\.?\d*)\s*([+\-*/])\s*(-?\d+\.?\d*)\s*$"
        match = re.match(pattern, expression.strip())
        if not match:
            raise ValueError("Invalid expression format")

        left_str, op, right_str = match.groups()
        return self.OPERATORS[op](float(left_str), float(right_str))

```

#### render


`pkg/render.py`

```python
class Render:
    def __init__(self, expression: str, result: float):
        self.expression = expression
        self.result = result

    def __str__(self) -> str:
        result = int(self.result) if self.result == int(self.result) else self.result
        # Format expression with spaces around operator
        formatted_expr = self.expression.replace("+", " + ").replace("-", " - ").replace("*", " * ").replace("/", " / ")
        formatted_expr = " ".join(formatted_expr.split())

        # Calculate dynamic width based on content
        content_width = max(len(formatted_expr), len(str(result)))
        width = max(content_width + 4, 9)  # minimum width 9

        # Build border lines
        top_bottom = "┌" + "─" * width + "┐"
        middle = "│" + " " * width + "│"
        eq_line = "│" + "=".center(width) + "│"
        expr_line = "│" + formatted_expr.center(width) + "│"
        result_line = "│" + str(result).center(width) + "│"

        lines = [
            top_bottom,
            expr_line,
            middle,
            eq_line,
            middle,
            result_line,
            top_bottom.replace("┌", "└").replace("┐", "┘"),
        ]
        return "\n".join(lines)


```

#### main

`main.py`

```python
import sys
from pkg.calculator import Calculator
from pkg.render import Render


def main():
    calculator = Calculator()
    if len(sys.argv) <= 1:
        print("Calculator App")
        print("Usage: python main.py '<expression>'")
        print("Example: python main.py '1+1'")
        return

    expression = " ".join(sys.argv[1:])
    try:
        result = calculator.calculate(expression)
        to_print = Render(expression, result)
        print(to_print)
    except Exception as e:
        print(f"Error: {e}")
        return


main()

```

#### test

`test.py`

```python
import unittest
from pkg.calculator import Calculator


class TestCalculator(unittest.TestCase):
    def setUp(self):
        self.calculator = Calculator()

    def test_addition(self):
        result = self.calculator.calculate("3+5")
        self.assertEqual(result, 8)

    def test_subtraction(self):
        result = self.calculator.calculate("1-1")
        self.assertEqual(result, 0)

    def test_multiplication(self):
        result = self.calculator.calculate("2*2")
        self.assertEqual(result, 4)

    def test_division(self):
        result = self.calculator.calculate("4/2")
        self.assertEqual(result, 2)

    def test_empty_expression(self):
        with self.assertRaises(ValueError):
            self.calculator.calculate("")

    def test_invalid_operator(self):
        with self.assertRaises(ValueError):
            self.calculator.calculate("$ 3 5")

    def test_not_enough_operands(self):
        with self.assertRaises(ValueError):
            self.calculator.calculate("3 +")


def main():
    unittest.main()


if __name__ == "__main__":
    main()

```
