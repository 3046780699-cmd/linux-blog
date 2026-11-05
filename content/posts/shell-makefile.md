---
title: "用 Shell 和 Makefile 整理重复任务"
date: 2026-11-06T00:02:00+08:00
draft: false
categories: ["Shell编程"]
tags: ["Linux", "Shell", "Makefile"]
summary: "整理 Shell 的输入验证和变量引用，理解 Makefile 中目标、依赖与命令的关系。"
---

## 先把输入说清楚

Shell 脚本的第一行通常写成 `#!/bin/bash`，用于说明执行它的解释器。编写脚本时，我更关注输入是否符合预期，而不是先堆叠很多命令。变量引用应使用双引号，以免空格和通配符造成意外拆分。

下面的例子检查一个年龄参数，先判断参数数量，再判断格式，最后使用整数比较。

```bash
#!/bin/bash
if [ "$#" -ne 1 ]; then
    printf 'Usage: %s AGE\n' "$0" >&2
    exit 1
fi
if [[ ! "$1" =~ ^[0-9]{1,3}$ ]]; then
    printf 'AGE must contain 1 to 3 digits\n' >&2
    exit 1
fi
age=$((10#$1))
if [ "$age" -ge 18 ]; then
    printf 'Adult\n'
else
    printf 'Under 18\n'
fi
```

`10#` 明确使用十进制，避免带前导零的输入被按其他进制解释。限制位数也能避免过大的输入超出预期。错误信息写入标准错误，退出码让调用者能够区分正常与失败。

## Makefile 关注依赖关系

对于由多个 C 文件组成的程序，Makefile 可以表达“目标需要哪些文件、怎样生成”。下面是一个最小示例。配方行开头必须是真正的 Tab，不能用若干空格替代。

```makefile
CC = gcc
CFLAGS = -Wall -g
OBJS = main.o hello.o

hello: $(OBJS)
	$(CC) $(CFLAGS) -o $@ $^

%.o: %.c hello.h
	$(CC) $(CFLAGS) -c $< -o $@

.PHONY: clean
clean:
	rm -f hello $(OBJS)
```

`$@` 表示目标，`$^` 表示全部依赖，`$<` 表示第一个依赖。声明头文件依赖后，修改 `hello.h` 就能使相应目标重新编译。若不写头文件依赖，编译结果可能与源码不一致。

## 两种工具如何配合

Shell 适合输入验证、文件操作与命令串联，Makefile 适合根据依赖和修改时间安排构建。把二者的职责分清楚后，脚本可以负责准备参数，Makefile 则负责选择需要重建的目标。

本文围绕课程指导书的 Shell 和 Makefile 章节整理。示例用于解释写法，博客搭建报告中的运行截图另按真实操作记录。
