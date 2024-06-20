---
title: "Langchain Chatchat 搭建问答知识库"
date: "2024-04-13"
tags: ["Langchain", "知识库", "RAG", "AI"]
categories: ["教程"]
summary: "Langchain Chatchat是一款非常方便的离线大语言模型知识库管理软件。"
---

# Langchain Chatchat 搭建问答知识库

Langchain Chatchat的回答效果怎么样呢？

现在我们就来试一试，通过本地的大语言模型来测试回答效果。

我们都是一样使用Ollama驱动的GGUF量化模型。测试的对象是120万字的《射雕英雄传》。

我们的问题包括：

1. 郭靖是谁？
2. 郭靖的父母是谁？
3. 郭啸天是谁？

即便对于熟悉金庸小说的读者而言，可能也不太了解郭靖的母亲母亲究竟是谁。我们尝试利用本地的ai来回答这些问题吧。

![chatglm3 (hosted by Ollama) + Langchain Chatchat 回答效果（5项引用）](/images/Langchain%20Chatchat%20%E6%90%AD%E5%BB%BA%E9%97%AE%E7%AD%94%E7%9F%A5%E8%AF%86%E5%BA%93/Untitled.png)

chatglm3 (hosted by Ollama) + Langchain Chatchat 回答效果（5项引用）

Chatglm3的回答教人喜忧参半。

![llama3-8b (hosted by Ollama) + Langchain Chatchat 回答效果（5项引用）](/images/Langchain%20Chatchat%20%E6%90%AD%E5%BB%BA%E9%97%AE%E7%AD%94%E7%9F%A5%E8%AF%86%E5%BA%93/Untitled%201.png)

llama3-8b (hosted by Ollama) + Langchain Chatchat 回答效果（5项引用）

llama3-8b 基本完败

![zephyr 7b  (hosted by Ollama) + Langchain Chatchat 回答效果（5项引用）](/images/Langchain%20Chatchat%20%E6%90%AD%E5%BB%BA%E9%97%AE%E7%AD%94%E7%9F%A5%E8%AF%86%E5%BA%93/Untitled%202.png)

zephyr 7b  (hosted by Ollama) + Langchain Chatchat 回答效果（5项引用）

zephyr 7b的回答 中文优化不太好 但是郭靖的父母完全答对了

![Untitled](/images/Langchain%20Chatchat%20%E6%90%AD%E5%BB%BA%E9%97%AE%E7%AD%94%E7%9F%A5%E8%AF%86%E5%BA%93/Untitled%203.png)

![Qwen 14b (hosted by Ollama) + Langchain Chatchat 回答效果（4项引用）](/images/Langchain%20Chatchat%20%E6%90%AD%E5%BB%BA%E9%97%AE%E7%AD%94%E7%9F%A5%E8%AF%86%E5%BA%93/Untitled%204.png)

Qwen 14b (hosted by Ollama) + Langchain Chatchat 回答效果（4项引用）