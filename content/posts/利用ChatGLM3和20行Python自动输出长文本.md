---
title: "AI自动写稿 利用ChatGLM3和Python自动输出长文本"
date: "2023-12-17"
tags: ["ChatGLM3", "Python", "自动写作", "AI", "LLM"]
categories: ["教程"]
summary: "本教程介绍了如何使用ChatGLM3和Python实现自动撰写稿件，提高写作效率。涵盖了本地大语言模型的优势、自动输出的局限性、长度限制问题以及如何通过设置系统提示词和对话结构来优化AI写作流程。"
---

# 利用ChatGLM3和20行Python自动输出长文本

更多AIGC教程，访问UP「码钉泥」的B站
[https://space.bilibili.com/650927704/](https://space.bilibili.com/650927704/)

经过前面的两篇教程的铺垫，我们今天可以进入正题：AI自动写稿。自动写稿可以帮助我们批量准备底稿，极大提高写作效率。

我们今天带大家进行简单的Python编写，实现利用本地的ChatGLM3大语言模型，实现自动撰写稿件的目的。

如果大家没有Python语言的编程基础也不用着急，因为这是一门非常容易入门的语言。

### 为什么选用本地大语言模型

在前面的教程中，我们选择了使用ChatGLM3搭建本地的大语言模型。在线模型已经够好用了，为什么还要搭建本地大语言模型？因为费用问题。

本地搭建最大的价值，不仅在于离线操作、保障数据安全，而且——它还可以进行几乎无限的本地API调用。

那么如果你选择采用像ChatGPT这样的在线大语言模型，进行API调用需要付费。尽管ChatGPT3.5的API调用费率并不算很高（1000 token ~ ￥0.014），但是它的支付门槛确实比较高的，那就是要求你有一张境外的信用卡。而选择本地离线的大远模型，则不会有成本方面的顾虑。

| Model | Usage |
| --- | --- |
| gpt-3.5-turbo | $0.002 / 1K tokens |

### 自动输出

大语言模型可以实现自动输出吗？简单的答案是：可以。

然而这种输出必然是存在局限的。自动输出的内容受限于大语言模型被训练时所获得的信息。如果你采用在线大语言模型，则不必担心信息的局限性；但是如果你采用本地大语言模型，则需要频繁对本地大语言模型进行更新，或者通过Lora微调的形式为它增加更新的信息。

### 长度限制

大语言模型一次输出内容的长度是有限的。

这是由于大语言模型在训练的过程中所采用的训练文本长度是有限的，另外transformer架构在进行长文本推理时也存在一些技术上的不足。这就好像你不能够要求一位小学三年级学生写出上万字篇幅的论文一样——他仅仅只熟悉篇幅为一两百字的日记的写法。

计算这种长度的单位叫做token。

在大语言模型中，Token指的是模型处理的最小单位，是一个“语义元素”，通常是一个单词（对应中文的词汇）、一个标点符号或者一个子词（子词指的构成英语单词、具有含义的前缀或后缀，对应中文词汇中的单字）。

大语言模型一般都使用token来计算消耗的运算资源。有一种粗略的换算比例，那就是每1000 Token.可以处理750个英文单词，或者500个左 Token除了 Token。

Token除了被用来用作计费单位，同样也用来标记大语言模型能够处理的上**下文的长度**。早期的ChatGPT能够处理的token数量是4000（4k），因为每一次新的对话都会附带之前对话的全文。因此，随着对话的深入，所消耗的token就会越来越大。因此，早期的大语言模型在对话进行到十几轮之后，便存在资源耗尽的困境。

现在已经有很多大语言模型能够轻松处理32k~128k的 token，可以一次性处理非常长的文本材料。月之暗面出品的Kimi甚至可以一次性处理200万字的文本材料。但是本地大语言模型可以处理的token长度，普遍都在8k左右。

### 解决方案

大模型就像一个健忘的秘书。他拥有长期记忆，却容易忽略当前对话的上下文。为了让它实现长文本写作，我们必须设置好分段输出的内容大纲。这一点在应用文写作上比较容易解决，但是如果放在文学创作上则显得力有不殆。因此我们仅仅讨论应用文的写作。关于文学写作，大家可以自行尝试摸索。

适合AI进行自动写作的应用文结构主要有两种：QA结构、总分总结构。

QA结构，也就是“问题-答案”结构，是一种非常适宜AI进行长文本输出的写作结构。我们的代码主要演示的就是这一类文体的自动写作。其技术核心就是1)设置系统提示词，2)拼接对话，3)输出内容，4)拼接成长文本文档。

## 技巧

第1步，设定人设。

```python
sys_prompt = [
    # 通过给role设置为system，给Chat模型的此处对话赋予角色定位
    {
        "role": "system",
        "content": "你是一位非常专业的财经专栏作家，擅长解读财经概念。"
    }
]
```

第2步，输出问题列表。

```python
big_question = sys_prompt + [{
        "role": "user", 
        "content": '我正在准备一篇专题报道，请帮我列出关于美国证监会最重要的10个问题。\
        请直接用数字列表列出这些问题。'
    }]
    
# 创建一个 GPT-3 请求
completion = client.chat.completions.create(
    model = "chatgpt-3.5-turbo",
    temperature = 0.75,
    top_p = 0.75,
    messages = msg
)
respond = completion.choices[0].message
print(respond)

# 输出如下
# 1. 是什么美国证监会的功能和作用？
# 2. 它的历史是什么？
# 3. 它的主要职责是什么？
# 4. 它如何监管美国的证券市场？
# 5. 它如何保护投资者利益？
# 6. 它如何确保公司的财务报告的准确性和公正性？
# 7. 它如何防止欺诈和操纵市场？
# 8. 它如何确定公司的股票价格是否公正？
# 9. 它如何监管证券交易？
# 10. 它如何监管股票市场的指数？
```

第3步，将问题列表中的问题重新输入给AI。

```python
i = 1
for line in respond.content.split('\n'):
	  if len(line)>1:
	      print(line)
	      save_result(line)
	      line = line.split('.',1)[1].strip()
	      q = sys_prompt + [{
	          "role": "user", 
	          "content": f"我正在准备一篇关于美国证监会的专题报道。"
	      },{
	          "role": "user", 
	          "content": line
	      }]
	      completion = client.chat.completions.create(
				    model = "chatgpt-3.5-turbo",
				    temperature = 0.75,
				    top_p = 0.75,
				    messages = q
				)
				respond = completion.choices[0].message
	      print(respond.content)
```

第4步，将输出的文本内容合并成txt文档或者md文档。

这里就不赘述了。请参考下面完整的代码。

## 代码

```python
import argparse
from openai import OpenAI
import time

parser = argparse.ArgumentParser()
parser.add_argument("-k", "--keyword", type=str, help="关键字")
parser.add_argument("-n", "--number", type=str, help="问题数量")
args = parser.parse_args()

client = OpenAI(
    api_key= "API Key",
    base_url= "http://127.0.0.1:8000/v1" # ChatGLM3本地端口

)

keyword = args.keyword if args.keyword else '上海' 
number = args.number if args.number else 20 
question = f'如果你是一名财经记者，请帮我列出关于{keyword}最重要的{number}个问题。请直接用数字列表列出这些问题。'

sys_prompt = [
    # 通过给role设置为system，给Chat模型的此处对话赋予角色定位
    {
        "role": "system",
        "content": "你是一位非常专业的财经专栏作家，擅长解读财经概念。"
    }
]

def request_completion(msg):
    # 创建一个 GPT-3 请求
    completion = client.chat.completions.create(
        model = "chatgpt-3.5-turbo",
        temperature = 0.75,
        top_p = 0.75,
        messages = msg
    )
    respond = completion.choices[0].message
    return respond

def save_result(content):
    with open('answer.txt', 'a+', encoding='utf-8') as f:
        f.write(content + '\n\n')

if __name__ == '__main__':

    t0 = time.perf_counter()

    print(question)
    save_result(question)

    big_question = sys_prompt + [{
        "role": "user", 
        "content": question
    }]
    outlines = request_completion(big_question)
    save_result(outlines.content)

    i = 1
    for line in outlines.content.split('\n'):
        if len(line)>1:
            print(line)
            save_result(line)
            line = line.split('.',1)[1].strip()
            q = sys_prompt + [{
                "role": "user", 
                "content": f"我们来讨论一下{keyword}。"
            },{
                "role": "user", 
                "content": line
            }]
            answer = request_completion(q)
            save_result(answer.content)

    t1 = time.perf_counter()
    time_lapse = round(t1-t0,2)
    print(f'回答结束！耗时{time_lapse}秒')

```