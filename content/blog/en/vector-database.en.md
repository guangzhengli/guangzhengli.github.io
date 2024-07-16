---
title: Vector Databases
date: 2023-07-15T15:15:00+08:00
tags: ["llm", "gpt", "vector", "database"]
series: ["GPT Development"]
featured: true
math: true
---

You might have recently heard news like a startup focusing on vector databases securing millions in investment just after finishing their PPT, or an open-source vector database making headlines on Hackernews due to its simplistic code. Over the past few months, the development of AI applications has been booming, driving the entire AI tech stack, with vector databases being one of the hottest topics.

Recently, while developing two open-source projects, [ChatFiles](https://github.com/guangzhengli/ChatFiles) and [VectorHub](https://github.com/guangzhengli/vectorhub), I delved into vector databases. After gaining a general understanding of mainstream vector databases and search algorithms, I decided to compile this knowledge into an article to help others.

<!--more-->

## GPT's Limitations

In the past few months, we have been in the midst of an AI revolution, with the most dazzling being the emergence of GPT-3.5/4. While GPT-3.5/4 has brought us immense amazement, its inherent limitations and numerous restrictions have also been a headache for developers. For instance, the input context (tokens) size limitation troubles many developers and consumers. The gpt-3.5-turbo model has a limit of 4K tokens (~3000 words), meaning users can input a maximum of 3000 words for GPT to understand and infer answers.

Some might wonder, "My ChatGPT has a conversation memory function. Since it can remember chats, the input token limit shouldn't matter. I can just split the text into multiple inputs, and it will naturally remember my previous conversations, thus bypassing the token limit."

This idea is not entirely correct. GPT, as an LLM model, does not have a memory function. The so-called memory function is just developers storing conversation records in memory or a database. When you send a message to the GPT model, the program automatically combines the recent few conversation records (based on the 4096 tokens limit) into the final question through a prompt and sends it to ChatGPT. In short, if your conversation memory exceeds 4096 tokens, it will forget the previous conversations. This is a current limitation of GPT in handling more complex tasks.

Different models have different token limits. GPT-4 has a limit of 32K tokens, while the largest token limit currently is [Claude's 100K](https://www.anthropic.com/index/100k-context-windows), meaning you can input about 75,000 words of context to GPT, allowing GPT to directly understand the entire content of a book like "Harry Potter" and answer related questions.

But does this solve all our problems? The answer is no. For instance, Claude's example shows that GPT's response time for 72K tokens of context is 22 seconds. If we have GB-level or larger documents that need GPT's understanding and Q&A, the current computing power is hard to provide a good experience. More critically, the current GPT API charges based on tokens, so the more context you input, the more expensive it gets.

This situation is somewhat similar to early developers facing the dilemma of developing applications with only a few MB or even KB of memory. One, 'memory' was expensive, and two, 'memory' was too small. So, until there are revolutionary advancements in GPT models in terms of performance, cost, and attention mechanisms, developers have to face the challenge of bypassing GPT's token limitations.

## The Rise of Vector Databases

Under the limitations of GPT models, developers have to look for other solutions, and vector databases are one of them. The core idea of vector databases is to convert text into vectors, then store the vectors in a database. When a user inputs a question, it is converted into a vector, then the database searches for the most similar vectors and context, and finally returns the text to the user.

When we have a document that needs GPT processing, such as a customer service training manual or an operation manual, we can first convert all the content of this document into vectors (this process is called Vector Embedding). Then, when a user asks a related question, we convert the user's search content into a vector, search for the most similar vectors in the database, match the most similar contexts, and finally return the context to GPT. This not only greatly reduces GPT's computation, thereby improving response speed, but more importantly, reduces costs and bypasses GPT's token limitations.

![Embedding](https://storage.guangzhengli.com/images/Embedding.png)

For example, if we have a long conversation with ChatGPT, we can save all the conversations as vectors. When we ask ChatGPT a question, we can convert the question into a vector to semantically search all past chat records, find the 'memory' most related to the current question, and send it to ChatGPT, greatly improving GPT's output quality.

The role of vector databases is not limited to text semantic search. In traditional AI and machine learning scenarios, it also includes functions like facial recognition, image search, and voice recognition. However, it is undeniable that the current boom in vector databases is because they greatly help AI in understanding and maintaining long-term memory to perform complex tasks. For example, you can try [LangChainJs's document search/Q&A function](https://js.langchain.com/docs/) to feel its charm, or try my open-source projects [VectorHub](https://github.com/guangzhengli/vectorhub) and [ChatFiles](https://github.com/guangzhengli/ChatFiles), where you can upload a document or base it on a web document and then ask related questions. These functions are all based on Vector Embedding and vector database products.

## Vector Embeddings

For traditional databases, search functions are implemented based on different indexing methods (B Tree, inverted index, etc.) combined with exact matching and ranking algorithms (BM25, TF-IDF). Essentially, it is based on exact text matching. This indexing and search algorithm is very suitable for keyword search functions but very weak for semantic search functions.

For example, if you search for "puppy," you can only get results related to the keyword "puppy" and not "Corgi," "Golden Retriever," etc., because "puppy" and "Golden Retriever" are different words. Traditional databases cannot recognize their semantic relationship, so traditional applications need to manually tag the features between "puppy" and "Golden Retriever" to achieve semantic search. This process of generating and selecting features is also known as Feature Engineering, which is the process of transforming raw data into features that better represent the essence of the problem.

However, if you need to handle unstructured data, you will find that the number of features of unstructured data will start to expand rapidly. For example, if we are dealing with images, audio, video, etc., this process becomes very difficult. For images, features like color, shape, texture, edges, objects, scenes, etc., can be labeled, but these features are too many and hard to label manually. So, we need an automated way to extract these features, which can be achieved through Vector Embedding.

Vector Embedding is generated by AI models (such as large language models LLM). It generates high-dimensional vector data representing different features of the data based on different algorithms. These features represent different dimensions of the data. For example, for text, these features may include vocabulary, grammar, semantics, sentiment, emotion, theme, context, etc. For audio, these features may include pitch, rhythm, pitch, timbre, volume, speech, music, etc.

For example, currently, text vectors can be generated through OpenAI's text-embedding-ada-002 model, image vectors can be generated through the clip-vit-base-patch32 model, and audio vectors can be generated through the wav2vec2-base-960h model. These vectors are all generated by AI models, so they all have semantic information.

For example, if we use the text-embedding-ada-002 model to embed the sentence "Your text string goes here," it will generate a 1536-dimensional vector, and the result will look like this: `“-0.006929283495992422, -0.005336422007530928, ... -4547132266452536e-05,-0.024047505110502243”`. This vector contains all the features of the sentence, including vocabulary and grammar, and we can store it in a vector database for subsequent semantic search.

## Features and Vectors

Although the core of vector databases is similarity search, before diving into similarity search, we need to understand the concepts and principles of features and vectors.

Let's think about a question: Why do we distinguish different objects and things in life?

From a theoretical perspective, this is because we recognize different features of different things to identify categories. For example, to distinguish different types of dogs, we can use features like body size, hair length, and nose length. As shown in the photo below, sorted by body size, you can see that the larger the dog, the closer it is to the right side of the coordinate axis. This way, we get a one-dimensional coordinate of the body size feature and the corresponding value, from 0 to 1, showing the position of each dog in the coordinate system.

![Snipaste_2023-07-15_20-55-09](https://storage.guangzhengli.com/images/Snipaste_2023-07-15_20-55-09.png)

However, relying on just one feature of body size is not enough. For example, the body sizes of Huskies, Golden Retrievers, and Labradors in the photo are very close, making it hard to distinguish them. So, we continue to observe other features, such as hair length.

![Snipaste_2023-07-15_20-59-13](https://storage.guangzhengli.com/images/Snipaste_2023-07-15_20-59-13.png)

This way, each dog corresponds to a two-dimensional coordinate point, and we can easily distinguish Huskies, Golden Retrievers, and Labradors. If we still can't distinguish German Shepherds and Rottweilers, we can continue to use other features, like nose length, to get a three-dimensional coordinate system and the position of each dog in the three-dimensional coordinate system.

In this case, as long as there are enough features, we can distinguish all dogs, and finally get a high-dimensional coordinate system. Although we can't imagine what a high-dimensional coordinate system looks like, in an array, we just need to keep adding numbers to the array.

In fact, as long as there are enough dimensions, we can distinguish all things. Everything in the world can be represented by a multi-dimensional coordinate system, and they all correspond to a coordinate point in a high-dimensional feature space.

So, what does this have to do with similarity search? You will find that in the two-dimensional coordinates above, the coordinates of German Shepherds and Rottweilers are very close, which means their features are also very close. We all know that vectors are mathematical structures with magnitude and direction, so we can represent these features with vectors and determine their similarity by calculating the distance between vectors. This is **similarity search**.

The above images and detailed explanations are from [this video](https://www.bilibili.com/video/BV11a4y1c7SW), which also includes some of the similarity search algorithms introduced below. If you are interested in vector databases, I highly recommend watching this video.

## Similarity Search

Now that we know we can determine the similarity of vectors by comparing their distances, how do we apply this to real-world scenarios? If we want to find the most similar vector to a given vector in a massive dataset, we need to compare each vector in the database, but this is computationally intensive. So, we need an efficient algorithm to solve this problem.

There are many efficient search algorithms, and their main idea is to improve search efficiency in two ways:

1. Reduce vector size—by dimensionality reduction or reducing the length of the representation vector.
2. Narrow the search range—by clustering or organizing vectors into tree or graph structures and limiting the search range to the closest cluster or filtering through the most similar branches.

First, let's introduce the core concept shared by most algorithms, which is clustering.

### K-Means and Faiss

After saving the vector data, we can first cluster the vector data. For example, in the two-dimensional coordinate system below, four cluster centers are defined, and each vector is assigned to the nearest cluster center. The clustering algorithm continuously adjusts the cluster center positions, dividing the vector data into four clusters. Each time we search, we only need to determine which cluster the search vector belongs to and then search within that cluster, reducing the search range from four clusters to one, greatly reducing the search range.

![kmeans](https://storage.guangzhengli.com/images/kmeans.png)

Common clustering algorithms include K-Means, which can divide data into k categories, where k is specified in advance. The basic steps of the k-means algorithm are:

1. Choose k initial cluster centers.
2. Assign each data point to the nearest cluster center.
3. Calculate the new center of each cluster.
4. Repeat steps 2 and 3 until the cluster centers no longer change or the maximum number of iterations is reached.

However, this search method also has some drawbacks. For example, if the search content is right in the middle of two classification areas, it is likely to miss the most similar vector.

In reality, the distribution of vectors is not as clear-cut as in the figure. The boundaries of regions are often adjacent, as shown in the [Faiss algorithm](https://github.com/facebookresearch/faiss) below.

We can imagine vectors as being contained within Voronoi cells. When a new query vector is introduced, the distance to the centroids is first measured, and the search range is limited to the cell containing the centroid.

![WUjl5M](https://storage.guangzhengli.com/images/WUjl5M.jpg)

To solve the problem of possible omissions during the search, the search range can be dynamically adjusted. For example, when nprobe = 1, only the nearest cluster center is searched. When nprobe = 2, the nearest two cluster centers are searched. The value of nprobe can be adjusted according to actual business needs.

![FZadSG](https://storage.guangzhengli.com/images/FZadSG.jpg)

In fact, except for brute-force search, which can perfectly find the nearest neighbor, all search algorithms can only make a trade-off between speed, quality, and memory. These algorithms are also known as Approximate Nearest Neighbor (ANN).

### Product Quantization (PQ)

In large-scale datasets, the biggest problem with clustering algorithms is the high memory consumption. This is mainly reflected in two aspects. First, because each vector's coordinates need to be saved, and each coordinate is a floating-point number, the memory consumption is already very large. In addition, the cluster centers and the cluster center index of each vector need to be maintained, which also consumes a lot of memory.

For the first problem, it can be solved by quantization, which is a common lossy compression method. For example, in memory, each vector in the cluster center can be represented by the cluster center vector, and a codebook of all vectors to the cluster center can be maintained, greatly reducing memory consumption.

But this still cannot solve all problems. In the previous example, the cluster centers were defined in a two-dimensional coordinate system. Similarly, in a high-dimensional coordinate system, multiple cluster centers can be defined, continuously adjusted, and iterated until multiple stable and convergent center points are found.

However, in high-dimensional coordinate systems, the curse of dimensionality problem will also be encountered. Specifically, as the dimensions increase, the distance between data points will grow exponentially. This means that in high-dimensional coordinate systems, more cluster centers are needed to divide the data points into smaller clusters to improve classification quality. Otherwise, the distance between the vector and its cluster center will be very far, greatly reducing search speed and quality.

But if you want to maintain classification and search quality, you need to maintain a large number of cluster centers. This brings another problem: the number of cluster centers will grow exponentially with the increase in dimensions, leading to a rapid increase in the size of the codebook, greatly increasing memory consumption. For example, a 128-dimensional vector needs to maintain 2^64 cluster centers to maintain a good quantization result, but the size of such a codebook has already exceeded the memory size of maintaining the original vector.

The solution to this problem is to decompose the vector into multiple sub-vectors and then quantize each sub-vector independently. For example, a 128-dimensional vector can be divided into 8 16-dimensional vectors, and then clustering is performed on the 8 16-dimensional sub-vectors. Since a 16-dimensional sub-vector only needs about 256 cluster centers to achieve a good quantization result, the size of the codebook can be reduced from 2^64 to 8 * 256 = 2048 cluster centers, thereby reducing memory consumption.

![5dAeV5](https://storage.guangzhengli.com/images/5dAeV5.jpg)

After encoding the vector, 8 encoding values are obtained, which are combined to form the final encoding value of the vector. When used, these 8 encoding values are searched in the 8 sub-codebooks to find the corresponding 16-dimensional vectors, which are then combined using the Cartesian product to form a 128-dimensional vector, obtaining the final search result. This is the principle of Product Quantization (PQ).

Using the PQ algorithm can significantly reduce memory consumption and speed up the search. Its only problem is that the search quality will decrease, but as we mentioned earlier, all algorithms make a trade-off between memory, speed, and quality.

### Hierarchical Navigable Small Worlds (HNSW)

In addition to clustering, approximate nearest neighbor search can also be achieved by constructing trees or graphs. The basic idea of this method is to find the nearest vector to the vector being added to the database and then connect them, forming a graph. When searching, start from a node in the graph, continuously perform nearest neighbor search and shortest path calculation until the most similar vector is found.

This algorithm can ensure search quality, but if all nodes in the graph are connected by the shortest path, as in the bottom layer of the graph, all nodes need to be traversed during the search.

![GD7ufK](https://storage.guangzhengli.com/images/GD7ufK.jpg)

The solution to this problem is similar to the common skip list algorithm. As shown in the figure below, to search a skip list, start from the highest level and move to the right along the longest "skip" edge. If the current node's value is greater than the value being searched for, we know we have passed the target, so we move to the previous node at the next level.

![wOu6JL](https://storage.guangzhengli.com/images/wOu6JL.jpg)

HNSW inherits the same hierarchical format, with the highest level having longer edges (for fast search) and the lower levels having shorter edges (for accurate search).

Specifically, the graph can be divided into multiple layers, each layer being a small world where nodes are interconnected. Each layer's nodes are also connected to the nodes of the previous layer.