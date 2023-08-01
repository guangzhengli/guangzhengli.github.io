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

本篇文章主要介绍的是 GPT Embeddings 相关应用的开发，在今年 4 月份的时候，笔者因为开发 [ChatFiles](https://github.com/guangzhengli/ChatFiles) 这个开源项目，从而学习了 GPT Embeddings 相关的技术知识，但是由于笔者的时间精力有限，所以一直没有机会将这些知识整理成一篇文章，直到最近笔者又因为有了新的想法，开源了 [VectorHub](https://github.com/guangzhengli/vectorhub) 这个同样基于 GPT Embeddings 技术的项目，进而对 GPT Embeddings 的技术知识有了更深入的了解，所以就有了这一篇分享。

<!--more-->

## 从 Prompt 开始

AI 应用开发在过去一段时间内吸引了众多开发者入场，除了大家所熟知的 ChatGPT 之外，还涌现了大量有实际价值的 AI 应用，例如基于 AI 的翻译类的应用如 [openai-translator](https://github.com/openai-translator/openai-translator)、[immersivetranslate](https://immersivetranslate.com/)，写作类的应用如 [Notion AI](https://www.notion.so/product/ai)，编程辅助类的应用如 [GitHub Copilot](https://github.com/features/copilot) 和 [GitHub Copilot Chat](https://docs.github.com/en/copilot/github-copilot-chat/using-github-copilot-chat?tool=vscode)。

这些应用有些是优化了原有的体验，如基于 GPT 的翻译的 [openai-translator](https://github.com/openai-translator/openai-translator)，翻译质量和阅读体验远胜于之前的机器翻译，还有些则是提供了之前无法实现的功能，如 [GitHub Copilot](https://github.com/features/copilot) 的代码补全和生成，还有像 [GitHub Copilot Chat](https://docs.github.com/en/copilot/github-copilot-chat/using-github-copilot-chat?tool=vscode) 提供回答编码相关问题、解释代码、生成单元测试、给错误代码提出修复意见等等在以前完全无法想象的功能。

这些应用在功能上虽然没有相似之处，但是在实现原理中，它们都是主要基于 GPT 的 Prompt(提示)实现。Prompt 指的是提供给模型的文本或指令，可以用来引导模型生成自然语言输出。它可以给模型提供上下文信息，对模型的输出结果至关重要。

我们知道 GPT（Generative Pre-trained Transformer）是一个推理模型，它主要基于预训练和微调两个阶段。

在预训练阶段使用一个大规模的语料库，例如维基百科、新闻文章、小说等，当我们输入一个 Prompt 给它，它会基于这个 Prompt 给出一个预测的结果，这个结果是基于它在预训练阶段学习到的知识，并通过概率生成一个个的单词组合而来。这也是为什么相同的 Prompt 输入，每次的结果都会有所不同，因为每次的结果都是基于概率生成的，这也是它被称为生成式 AI 的原因。

在预训练完成后，在微调阶段会将 GPT 模型加载到特定的任务上，并使用该任务的数据集对模型进行训练。这样，模型就可以根据任务的要求进行微调，以便更好地理解 Prompt 并生成与任务相关的文本。通过微调，GPT 可以适应不同的任务，如文本分类、情感分析、问答系统等。



上一篇中，我们主要介绍了[向量数据库]({{< ref "blog/zh/vector-database" >}})，而


## References

- https://github.com/guangzhengli/ChatFiles
- https://github.com/guangzhengli/vectorhub