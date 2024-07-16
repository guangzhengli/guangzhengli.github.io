---
title: Vector Databases
date: 2023-07-15T15:15:00+08:00
tags: ["llm", "gpt", "vector", "database"]
series: ["GPT Development"]
featured: true
math: true
---

You might have recently heard news like a startup focusing on vector databases securing millions in investment just after finishing their PPT, or an open-source vector database making headlines on Hackernews due to its simplistic code. Over the past few months, the development of AI applications has been booming, driving the entire AI tech stack, with vector databases being one of the hottest topics.

Recently, while developing an open-source projects, [ChatFiles](https://github.com/guangzhengli/ChatFiles), I delved into vector databases. After gaining a general understanding of mainstream vector databases and search algorithms, I decided to compile this knowledge into an article to help others.

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

Apart from clustering, approximate nearest neighbor search can also be achieved by constructing trees or graphs. The basic idea is to add vectors to the database by first finding the nearest vector and then connecting them, forming a graph. When searching, you can start from a node in the graph and continuously perform nearest neighbor searches and shortest path calculations until you find the most similar vector.

This algorithm ensures search quality, but if all nodes in the graph are connected by the shortest path, as in the bottom layer of the graph, you still need to traverse all nodes during the search.

![GD7ufK](https://storage.guangzhengli.com/images/GD7ufK.jpg)

To solve this problem, the approach is similar to the common skip list algorithm. As shown in the figure, to search a skip list, you start from the highest level and move right along the longest "skip" edges. If the current node's value exceeds the target, you move down one level and continue.

![wOu6JL](https://storage.guangzhengli.com/images/wOu6JL.jpg)

HNSW inherits the same layered format, with longer edges at the top layer for fast searches and shorter edges at the lower layers for accurate searches.

Specifically, the graph can be divided into multiple layers, each being a small world where nodes are interconnected. Each layer's nodes connect to the nodes in the layer above. When searching, you start from the top layer, where the distances between nodes are long, reducing search time, and then search layer by layer downwards. The bottom layer ensures search quality by connecting similar nodes, allowing you to find the most similar vector.

If you're interested in skip lists and HNSW, check out [this video](https://www.youtube.com/watch?v=QvKMwLjdK-s&t=168s&ab_channel=JamesBriggs).

The HNSW algorithm is a classic space-for-time algorithm, offering high search quality and speed but with significant memory overhead. This is because it needs to store all vectors in memory and maintain a graph structure. Therefore, the choice of this algorithm depends on the actual scenario.

### Locality Sensitive Hashing (LSH)

Locality Sensitive Hashing (LSH) is another indexing technique for approximate nearest neighbor search. It is fast and provides approximate, non-exhaustive results. LSH uses a set of hash functions to map similar vectors into "buckets," so similar vectors have the same hash value. This allows you to determine vector similarity by comparing hash values.

Typically, we design hash algorithms to minimize hash collisions because the search time complexity of hash functions is O(1). However, if a hash collision occurs, meaning two different keys are mapped to the same bucket, data structures like linked lists are used to resolve conflicts, making the search time complexity O(n), where n is the length of the list. To improve the efficiency of hash functions, we usually minimize the probability of collisions.

But in vector search, our goal is to find similar vectors, so we can design a hash function that maximizes the probability of collisions for similar vectors. This way, similar vectors are mapped to the same bucket.

When searching for a specific vector, the same hash function is used to "bucket" similar vectors into a hash table. The query vector is hashed into a specific table, and then compared with other vectors in that table to find the closest match. This method is much faster than searching the entire dataset because each hash table bucket contains far fewer vectors than the entire space.

How should this hash function be designed? To better understand, let's start with a 2D coordinate system. As shown below, a random line can divide the 2D coordinate system into two regions, allowing us to determine similarity by checking if vectors are on the same side of the line. For example, the figure below shows four randomly generated lines, allowing a vector's position to be represented by four binary numbers, such as A and B being in the same region.

![lsh2](https://storage.guangzhengli.com/images/lsh2.png)

The principle is simple: if two vectors are close, they are more likely to be on the same side of the line. For example, the probability of a line passing through AC is much higher than passing through AB, so the probability of AB being on the same side is much higher than AC.

When searching for a vector, the vector is hashed again, and vectors in the same bucket are found. Then, a brute-force search is used to find the closest vector. As shown below, if a vector is hashed to 10, similar vectors like D in the same bucket are found, greatly reducing search time.

![lsh3](https://storage.guangzhengli.com/images/lsh3.png)

For more details on the LSH algorithm, refer to [this blog](https://www.pinecone.io/learn/series/faiss/locality-sensitive-hashing/).

#### Random Projection for LSH

If a random line can distinguish similarity in a 2D coordinate system, similarly, a random plane can divide a 3D coordinate system into two regions. In a multi-dimensional coordinate system, a random hyperplane can divide the space into two regions to distinguish similarity.

However, in high-dimensional space, the distance between data points is often very sparse, and the distance increases exponentially with dimensions. This results in many buckets, with the extreme case being one vector per bucket, making computation very slow. Therefore, in practice, LSH algorithms use random projection to project high-dimensional data points into lower-dimensional space, reducing computation time and improving query quality.

The basic idea behind random projection is to use a random projection matrix to project high-dimensional vectors into lower-dimensional space. A matrix of random numbers is created, with the size being the desired target low-dimensional value. Then, the dot product between the input vector and the matrix is calculated, resulting in a projected matrix with fewer dimensions but retaining their similarity.

When querying, the same projection matrix is used to project the query vector into lower-dimensional space. The projected query vector is then compared with the projected vectors in the database to find the nearest neighbor. Since the data dimensions are reduced, the search process is much faster than searching the entire high-dimensional space.

The basic steps are:

1. Randomly select a hyperplane from the high-dimensional space and project data points onto it.
2. Repeat step 1, selecting multiple hyperplanes and projecting data points onto them.
3. Combine the projection results of multiple hyperplanes into a vector representing the low-dimensional space.
4. Use a hash function to map the vectors in the low-dimensional space into hash buckets.

Similarly, random projection is an approximate method, and the projection quality depends on the projection matrix. Generally, the more random the projection matrix, the better the mapping quality. However, generating truly random projection matrices can be computationally expensive, especially for large datasets. For more details on the RP for LSH algorithm, refer to [this blog](https://www.pinecone.io/learn/series/faiss/locality-sensitive-hashing-random-projection/).

## Similarity Measurement

We have discussed different search algorithms for vector databases, but not how to measure similarity. In similarity search, the distance between two vectors is calculated to determine their similarity.

How do we calculate the distance between vectors in high-dimensional space? There are three common vector similarity algorithms: Euclidean distance, cosine similarity, and dot product similarity.

### Euclidean Distance

Euclidean distance refers to the distance between two vectors, calculated as:

$$d(\mathbf{A}, \mathbf{B}) = \sqrt{\sum_{i=1}^{n}(A_i - B_i)^2}$$

where $\mathbf{A}$ and $\mathbf{B}$ represent two vectors, and $n$ represents the vector's dimensions.

![KPhJ27](https://storage.guangzhengli.com/images/KPhJ27.jpg)

The advantage of the Euclidean distance algorithm is that it reflects the absolute distance between vectors, suitable for similarity calculations that consider vector length. For example, in recommendation systems, the number of historical user behaviors needs to be considered, not just the similarity of behaviors.

### Cosine Similarity

Cosine similarity refers to the cosine value of the angle between two vectors, calculated as:

$$\cos(\theta) = \frac{\mathbf{A} \cdot \mathbf{B}}{|\mathbf{A}| |\mathbf{B}|}$$

where $\mathbf{A}$ and $\mathbf{B}$ represent two vectors, $\cdot$ represents the dot product, and $|\mathbf{A}|$ and $|\mathbf{B}|$ represent the magnitudes of the vectors.

![fHLAfz](https://storage.guangzhengli.com/images/fHLAfz.jpg)

Cosine similarity is insensitive to vector length and only focuses on direction, making it suitable for high-dimensional vector similarity calculations, such as semantic search and document classification.

### Dot Product Similarity

Dot product similarity refers to the dot product value between two vectors, calculated as:

$$\mathbf{A} \cdot \mathbf{B} = \sum_{i=1}^{n}A_i B_i$$

where $\mathbf{A}$ and $\mathbf{B}$ represent two vectors, and $n$ represents the vector's dimensions.

![kyA3AN](https://storage.guangzhengli.com/images/kyA3AN.jpg)

The advantage of the dot product similarity algorithm is its simplicity, fast computation, and consideration of both vector length and direction. It is suitable for many practical scenarios, such as image recognition, semantic search, and document classification. However, the dot product similarity algorithm is sensitive to vector length, which may cause issues when calculating high-dimensional vector similarity.

Each similarity measurement algorithm has its advantages and disadvantages, and developers need to choose based on their data characteristics and business scenarios.

## Filtering

In practical business scenarios, similarity searches are often not needed across the entire vector database but are filtered by some business fields before querying. Therefore, vectors stored in the database often need to include metadata, such as user ID, document ID, etc. This allows filtering search results based on metadata to get the final result.

To achieve this, vector databases typically maintain two indexes: a vector index and a metadata index. Metadata filtering is performed before or after the similarity search itself, but in either case, it can slow down the query process.

![VwZxFW](https://storage.guangzhengli.com/images/VwZxFW.jpg)

The filtering process can be performed before or after the vector search itself, but each method has its challenges and may affect query performance:

- Pre-filtering: Metadata filtering is performed before the vector search. While this can help reduce the search space, it may cause the system to ignore relevant results that do not match the metadata filtering criteria.

- Post-filtering: Metadata filtering is performed after the vector search is completed. This ensures that all relevant results are considered, and irrelevant results are filtered out after the search.

To optimize the filtering process, vector databases use various techniques, such as advanced indexing methods for metadata or parallel processing to speed up filtering tasks. Balancing search performance and filtering accuracy is crucial for providing efficient and relevant vector database query results.

## Choosing a Vector Database

In this article, I have spent a lot of time introducing the principles and implementation of similarity search algorithms in vector databases. While similarity search algorithms are a core and key point of a vector database, other factors need to be considered in practical business scenarios, such as the database's availability, scalability, security, whether the code is open source, and the community's activity.

### Distributed

A mature vector database often needs to support distributed deployment to meet the storage and query needs of large-scale data. The more data you have, the more nodes you need, and the more errors and failures you encounter. Therefore, a distributed vector database needs to have high availability and fault tolerance.

High availability and fault tolerance in databases often require implementing sharding and replication capabilities. In traditional databases, sharding is often done based on the primary key or business needs. However, in distributed vector databases, sharding needs to consider vector similarity to ensure query quality and speed.

Other factors, such as consistency of replicated node data, data security, etc., also need to be considered in distributed vector databases.

### Access Control and Backup

In addition, whether the access control design is sufficient is another factor to consider. For example, when an organization and business grow rapidly, can new users and permissions be quickly added? Can new nodes be quickly added? Are audit logs complete? These are all factors to consider.

Moreover, database monitoring and backup are also important factors. When data fails, being able to quickly locate problems and restore data is a must for a mature vector database.

### API & SDK

Compared to the above factors, API & SDK may often be overlooked, but in practical business scenarios, API & SDK are often the most concerned factors for developers. The design of API & SDK directly affects the development efficiency and user experience of developers. A well-designed API & SDK can adapt to different needs. Vector databases are a new field, and this point is easily overlooked when most people are not clear about the needs in this area.

### Selection

As of now, the following are some vector database options:

| Vector Database | URL | GitHub Star | Language | Cloud |
| --- | --- | --- | --- | --- |
| chroma | https://github.com/chroma-core/chroma | 7.4K | Python | ❌ |
| milvus | https://github.com/milvus-io/milvus | 21.5K | Go/Python/C++ | ✅ |
| pinecone | https://www.pinecone.io/ | ❌ | ❌ | ✅ |
| qdrant | https://github.com/qdrant/qdrant | 11.8K | Rust | ✅ |
| typesense | https://github.com/typesense/typesense | 12.9K | C++ | ❌ |
| weaviate | https://github.com/weaviate/weaviate | 6.9K | Go | ✅ |

### Extending Traditional Data

Besides choosing a specialized vector database, extending traditional databases is also an option. For example, Redis, besides its traditional Key-Value database use, offers Redis Modules, which extend Redis with new features, commands, and data types. For example, the [RediSearch](https://redis.io/docs/interact/search-and-query/) module extends vector search capabilities.

Similarly, PostgreSQL extensions can extend database functionality. For example, [pgvector](https://github.com/pgvector/pgvector) enables vector search capabilities. It supports exact and similarity searches, cosine similarity, and other similarity measurement algorithms. Most importantly, it is attached to PostgreSQL, allowing you to leverage all PostgreSQL features, such as ACID transactions, concurrency control, backup, and recovery. It also has all PostgreSQL client libraries, allowing access from any language's PostgreSQL client, reducing developer learning and service maintenance costs.

For example, my open-source projects [ChatFiles](https://github.com/guangzhengli/ChatFiles) and [VectorHub](https://github.com/guangzhengli/vectorhub) currently use pgvector to implement vector search for GPT document Q&A, based on [Supabase's PostgreSQL + pgvector](https://supabase.com/blog/openai-embeddings-postgres-vector) service.

## Summary

This article mainly introduces the principles and implementation of vector databases, including basic concepts, similarity search algorithms, similarity measurement algorithms, filtering algorithms, and vector database selection. Vector databases are a new field, and most vector database companies' valuations are rapidly growing with the AI and GPT wave. However, in practical business scenarios, the application scenarios of vector databases are still relatively few. Developers and business experts need to explore the application scenarios of vector databases beyond the hype.

## References

- https://www.bilibili.com/video/BV11a4y1c7SW
- https://www.bilibili.com/video/BV1BM4y177Dk
- https://www.pinecone.io/learn/vector-database/
- https://github.com/guangzhengli/ChatFiles
- https://github.com/guangzhengli/vectorhub
- https://www.anthropic.com/index/100k-context-windows
- https://js.langchain.com/docs/
- https://www.pinecone.io/learn/series/faiss/locality-sensitive-hashing/
- https://www.pinecone.io/learn/series/faiss/product-quantization/
- https://www.pinecone.io/learn/series/faiss/locality-sensitive-hashing-random-projection/
- https://www.youtube.com/watch?v=QvKMwLjdK-s&t=168s&ab_channel=JamesBriggs
- https://www.pinecone.io/learn/series/faiss/faiss-tutorial/
- https://www.youtube.com/watch?v=sKyvsdEv6rk&ab_channel=JamesBriggs
- https://www.pinecone.io/learn/vector-similarity/
- https://github.com/chroma-core/chroma
- https://github.com/milvus-io/milvus
- https://www.pinecone.io/
- https://github.com/qdrant/qdrant
- https://github.com/typesense/typesense
- https://github.com/weaviate/weaviate
- https://redis.io/docs/interact/search-and-query/
- https://github.com/pgvector/pgvector