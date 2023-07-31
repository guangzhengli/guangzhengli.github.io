---
title: GPT Embeddings 应用开发和思考
date: 2023-07-31T20:10:00+08:00
tags: ["llm", "gpt", "vector", "embeddings", "database", "chatbot"]
series: ["GPT Development"]
featured: true
math: true
---

在过去几个月的时间中，我们似乎正处于人工智能的革命中。除了大多数人了解的 OpenAI ChatGPT 之外，许多非常新颖、有趣、实用的 AI 应用也是层出不穷，并且在使用这些应用时时，笔者也确确实实的感受到了生产力的提高。

但是关于 GPT 应用的开发知识和路线，目前似乎还没有太多的资料，所以笔者决定将自己的一些经验和思考整理成一个系列，希望能够帮助到大家。

本篇文章主要介绍的是 GPT Embeddings 相关应用的开发，在今年 4 月份的时候，笔者因为开发 [ChatFiles](https://github.com/guangzhengli/ChatFiles) 这个开源项目，从而学习了 GPT Embeddings 相关的技术知识，但是由于笔者的时间精力有限，所以一直没有机会将这些知识整理成一篇文章，直到最近笔者又因为有了新的想法，开源了 [VectorHub](https://github.com/guangzhengli/vectorhub) 这个同样基于 GPT Embeddings 技术的项目，对 GPT Embeddings 的技术知识有了更深入的了解，所以就有了这一篇分享。

<!--more-->

## 从 Prompt 开始

AI 应用开发在过去一段时间内吸引了众多开发者入场，除了大家所熟知的 ChatGPT 之外，还涌现了大量有实际价值的 AI 应用，例如基于 AI 的翻译类的应用如 [openai-translator](https://github.com/openai-translator/openai-translator)、[immersivetranslate](https://immersivetranslate.com/)，写作类的应用如 [Notion AI](https://www.notion.so/product/ai)，编程辅助类的应用如 [GitHub Copilot](https://github.com/features/copilot) 和 [GitHub Copilot Chat](https://docs.github.com/en/copilot/github-copilot-chat/using-github-copilot-chat?tool=vscode)。

这些应用有些是优化了原有的体验，如基于 GPT 的翻译的 [openai-translator](https://github.com/openai-translator/openai-translator)，翻译质量和阅读体验远胜于之前的机器翻译，还有些则是提供了之前无法实现的功能，如 [GitHub Copilot](https://github.com/features/copilot) 的代码补全和生成，还有像 [GitHub Copilot Chat](https://docs.github.com/en/copilot/github-copilot-chat/using-github-copilot-chat?tool=vscode) 提供回答编码相关问题、解释代码、生成单元测试、给错误代码提出修复意见等等在以前完全无法想象的功能。

这些应用在功能上虽然没有相似之处，但是在实现原理中，它们都是主要基于 GPT 的 Prompt 实现。我们知道 GPT 是一个推理模型，它基于



上一篇中，我们主要介绍了[向量数据库]({{< ref "blog/zh/vector-database" >}})，而


## References

- https://github.com/guangzhengli/ChatFiles
- https://github.com/guangzhengli/vectorhub