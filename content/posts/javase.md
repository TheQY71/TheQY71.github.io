+++
date = '2025-08-28T13:31:44+08:00'
draft = true
title = 'JavaSE 学习笔记'
categories = ["java"]
tags = ["java", "基础","学习笔记“]
+++

# 基础语法

## while

```java
int score = 2;
//直接让grade接受switch的结果
char grade = switch (score) {
    case 10, 9 -> 'A'; //case后面直接使用->来指定返回结果
    case 8 -> 'B';
    case 6, 7 -> 'C'; //当存在多个匹配条件时，使用逗号分隔
    default -> {
        System.out.println("继续努力💪");
        yield 'D';
    }
}; //别忘了这种写法相当于赋值，最后需要加分号
System.out.println("学生等级为: " + grade);
```