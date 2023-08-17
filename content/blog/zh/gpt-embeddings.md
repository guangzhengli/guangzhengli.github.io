---
title: GPT 应用开发和思考
date: 2023-07-31T20:10:00+08:00
tags: ["llm", "gpt", "vector", "embeddings", "database", "chatbot"]
series: ["GPT Development"]
featured: true
math: true
---

在过去几个月的时间中，我们似乎正处于人工智能的革命中。除了大多数人了解的 OpenAI ChatGPT 之外，许多非常新颖、有趣、实用的 AI 应用也是层出不穷，并且在使用这些应用时时，笔者也确确实实的感受到了生产力的提高。

但是关于 GPT 应用的开发知识和路线，目前似乎还没有太多的资料，所以笔者决定将自己的一些经验和思考整理成一个系列，希望能够帮助到大家。

本篇文章主要介绍的是 GPT 相关应用的开发思考，在今年 4 月份的时候，笔者因为开发 [ChatFiles](https://github.com/guangzhengli/ChatFiles) 这个开源项目，从而学习了 GPT 相关的技术知识，但是由于笔者的时间精力有限，所以一直没有机会将这些知识整理成一篇文章，直到最近笔者又因为有了新的想法，开源了 [VectorHub](https://github.com/guangzhengli/vectorhub) 这个同样基于 GPT Prompt 和 Embeddings 技术的项目，进而对 GPT 和 Embeddings 等技术知识有了更深入的了解，所以就有了这一篇分享。

<!--more-->

## 从 Prompt 开始

AI 应用开发在过去一段时间内吸引了众多开发者入场，除了大家所熟知的 ChatGPT 之外，还涌现了大量有实际价值的 AI 应用，例如基于 AI 的翻译类的应用如 [openai-translator](https://github.com/openai-translator/openai-translator)、[immersivetranslate](https://immersivetranslate.com/)，写作类的应用如 [Notion AI](https://www.notion.so/product/ai)，编程辅助类的应用如 [GitHub Copilot](https://github.com/features/copilot) 和 [GitHub Copilot Chat](https://docs.github.com/en/copilot/github-copilot-chat/using-github-copilot-chat?tool=vscode)。

这些应用有些是优化了原有的体验，如基于 GPT 的翻译的 [openai-translator](https://github.com/openai-translator/openai-translator)，翻译质量和阅读体验远胜于之前的机器翻译，还有些则是提供了之前无法实现的功能，如 [GitHub Copilot](https://github.com/features/copilot) 的代码补全和生成，还有像 [GitHub Copilot Chat](https://docs.github.com/en/copilot/github-copilot-chat/using-github-copilot-chat?tool=vscode) 提供回答编码相关问题、解释代码、生成单元测试、给错误代码提出修复意见等等功能，这些功能的实现难度在以前是完全无法想象的。

这些应用在功能上虽然没有相似之处，但是在实现原理中，它们都是主要基于 GPT 的 Prompt(提示)实现。Prompt 指的是提供给模型的文本或指令，可以用来引导模型生成自然语言输出(Completion)。它可以给模型提供上下文信息，对模型的输出结果至关重要。

我们知道 GPT（Generative Pre-trained Transformer）是一个推理模型，它主要基于预训练和微调两个阶段。

在预训练阶段使用一个大规模的语料库，例如维基百科、新闻文章、小说等，当我们输入一个 Prompt 给它，它会基于这个 Prompt 给出一个预测的结果，这个结果是基于它在预训练阶段学习到的知识，并通过概率生成一个个的单词组合而来。这也是为什么相同的 Prompt 输入，每次的结果都会有所不同，因为每次的结果都是基于概率生成的，这也是它被称为生成式 AI 的原因。

在预训练完成后，在微调阶段会将 GPT 模型加载到特定的任务上，并使用该任务的数据集对模型进行训练。这样，模型就可以根据任务的要求进行微调，以便更好地理解 Prompt 并生成与任务相关的文本。通过微调，GPT 可以适应不同的任务，如文本分类、情感分析、问答系统等。

所以这也就能理解为什么 Prompt 对于 GPT 应用开发的重要性，因为它是除了微调以外，我们能与 GPT 模型唯一的交互方式（当然除此之外还可以通过调整模型的 temperature 和 top_p 两个配置来控制 GPT 更多样化或更具创造性的输出，不过对于输出质量和对下游任务的处理能力并无明显影响）。所以 Prompt 是 GPT 应用开发最核心的部分，也是最需要开发者去思考和优化的部分。

## Prompt 学习路线

关于 Prompt 的基础知识，可以先去看看吴恩达老师的 [ChatGPT Prompt 工程](https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/)。可以通过两个小时不到的视频可以快速的了解 Prompt 的使用方式和它的魅力所在。

在有了一个初步的了解之后，笔者推荐 [Prompt Engineering Guide](https://www.promptingguide.ai/) 这份文档，该文档包含了大量的 Prompt 基础知识和未来的发展方向，对于 GPT 应用开发者来说，除了学习 Prompt 的基础知识之外，还可以从中获取到一些工程界和学术界对于 Prompt 的发展方向的思考，对于开发 AI 应用来说，这些思考弥足珍贵。

最后，非常推荐大家去看看 OpenAI 官方的 [GPT 最佳实践](https://platform.openai.com/docs/guides/gpt-best-practices) 这份文档，它是由 OpenAI 官方提供的 GPT 最佳实践指南，里面包含了大量的 Prompt 示例和使用技巧，对于 GPT 应用开发者来说，是一份非常有价值的文档。因为它是 OpenAI 官方通过合作伙伴或者 Hackathon 等不同的方式，在不同的业务领域 GPT 应用开发中总结出来的最佳实践，对于开发者来讲非常有启发价值！

## Prompt 最佳实践

关于 Prompt 编写的最佳实践，最为推荐的当然是 OpenAI 官方出品的 [GPT 最佳实践](https://platform.openai.com/docs/guides/gpt-best-practices) 这份文档，但对于开发 GPT 应用来讲，笔者还是想结合这份最佳实践和一些自己的经验，给大家分享一些 GPT 开发的实践。

### 清晰和详细

现实中大部分开发者在平常使用 GPT 的时候，都是以解决编程问题或者询问问题为主，所以容易带入以往使用 Google 等搜索引擎的经验来使用和开发 GPT。

例如当你想知道如何用 Python 编写斐波那契数列的时候，如果在以前使用 Google 搜索引擎，你可能会输入 `python fibonacci`。因为这样就足够了，Google 是基于倒排索引和 PageRank 算法的，只需要输入关键字，就能得到高质量的网页答案。

所以只需要这种输入两个字的输入方式是最简单和最高效的，毕竟就算多输入几个字 `how to write python fibonacci` ，对于 Google 搜索引擎来讲，输出质量是相差不大的。

而如果你使用的是 GPT，像 `python fibonacci` 这样的输入对于 GPT 来说是非常不友好的，因为它无法清晰的理解你的意图，所以它可能会给出一些不相关的结果（根据不同模型的质量会略有差异）。

但如果你输入的是 `编写一个用 python 函数来高效地计算 fibonacci 数列。评论每一行代码以解释每一部分的作用以及为什么这样写`。这样的输入对于 GPT 来说就非常清晰和详细，它能够清晰的理解你的意图，所以它给出的结果也会更加准确，**在保证输出质量下限的同时，还可以提高输出质量的上限！**

这和开发者以往使用 Google 等搜索引擎的经验是完全不同的，也是 GPT 开发者和使用者最容易忽视的地方。笔者在今年很早的时候开发 [ChatFiles](https://github.com/guangzhengli/ChatFiles) 项目时，曾匿名收集过使用者的 Prompt，可以说 95% 以上的使用者使用的都是非常简陋的 Prompt，甚至看起来有一种一字千金的感觉。所以当开发者开发 GPT 应用时，一定要注意 Prompt 的清晰和详细，这是保证 GPT 应用质量的关键。

未完待续......

## GPT Embedding 应用开发

上一篇中，我们主要介绍了[向量数据库]({{< ref "blog/zh/vector-database" >}})，


## References

- https://github.com/guangzhengli/ChatFiles
- https://github.com/guangzhengli/vectorhub