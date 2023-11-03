## RAG

> https://blog.langchain.dev/semi-structured-multi-modal-rag
> https://python.langchain.com/docs/modules/data_connection/retrievers/multi_vector?ref=blog.langchain.dev
> https://unstructured-io.github.io/unstructured/introduction.html#key-concepts
> https://www.anyscale.com/blog/a-comprehensive-guide-for-building-rag-based-llm-applications-part-1



LLMs can acquire new information in at least two ways: 

- (1) weight updates (e.g., fine-tuning)

- (2) RAG (retrieval augmented generation), which passes relevant context to the LLM via prompt.

---

A number of techniques to improve RAG have been developed:

| **Idea**                     | **Example**                                                  | **Sources**                                                  |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Base case** **RAG**        | Top K retrieval on embedded document chunks, return doc chunks for LLM context window | [LangChain vectorstores](https://python.langchain.com/docs/modules/data_connection/vectorstores/?ref=blog.langchain.dev), [embedding models](https://python.langchain.com/docs/modules/data_connection/text_embedding/?ref=blog.langchain.dev) |
| **Summary** **embedding**    | Top K retrieval on embedded document summaries, but return full doc for LLM context window | [LangChain Multi Vector Retriever](https://twitter.com/hwchase17/status/1695078249788027071?s=20&ref=blog.langchain.dev) |
| **Windowing**                | Top K retrieval on embedded chunks or sentences, but return expanded window or full doc | [LangChain Parent Document Retriever](https://twitter.com/hwchase17/status/1691179199594364928?s=20&ref=blog.langchain.dev) |
| **Metadata filtering**       | Top K retrieval with chunks filtered by metadata             | [Self-query retriever](https://python.langchain.com/docs/modules/data_connection/retrievers/self_query?ref=blog.langchain.dev) |
| **Fine-tune RAG embeddings** | Fine-tune embedding model on your data                       | [LangChain fine-tuning guide](https://blog.langchain.dev/using-langsmith-to-support-fine-tuning-of-open-source-llms/) |
| **2-stage** **RAG**          | First stage keyword search followed by second stage semantic Top K retrieval |                                                              |

---

##### Multi-Vector Retriever

Decouple documents, which we want to use for answer synthesis, from a reference, which we want to use for retriever. 

![img](/Users/anton/MyDocuments/Notes/machine-learning/.rag-images/mvr_overview.png)

##### Semi-Structured Data

We generate [summaries](https://python.langchain.com/docs/modules/data_connection/retrievers/multi_vector?ref=blog.langchain.dev#summary) of table elements, which is better suited to natural language retrieval.

![img](/Users/anton/MyDocuments/Notes/machine-learning/.rag-images/image-16.png)

##### Multi-Modal Data

There are at least three ways to approach the problem, which utilize the multi-vector retriever framework:

- Option 1:
  - Use multimodal embeddings (such as [CLIP](https://openai.com/research/clip)) to embed images and text
  - Retrieve both using similarity search
  - Pass raw images and text chunks to a multimodal LLM for answer synthesis
- Option 2:
  - Use a multimodal LLM (such as [GPT4-V](https://openai.com/research/gpt-4v-system-card), [LLaVA](https://llava.hliu.cc/), or [FUYU-8b](https://www.adept.ai/blog/fuyu-8b)) to produce text summaries from images
  - Embed and retrieve text
  - Pass text chunks to an LLM for answer synthesis

- Option 3:
  - Use a multimodal LLM (such as [GPT4-V](https://openai.com/research/gpt-4v-system-card), [LLaVA](https://llava.hliu.cc/), or [FUYU-8b](https://www.adept.ai/blog/fuyu-8b)) to produce text summaries from images
  - Embed and retrieve image summaries with a reference to the raw image
  - Pass raw images and text chunks to a multimodal LLM for answer synthesis


![img](/Users/anton/MyDocuments/Notes/machine-learning/.rag-images/image-22.png)

---

##### RAG workflow

1. **Data ingestion**: The first step is acquiring data from your relevant sources. At Unstructured we make this super easy with our [data connectors](https://unstructured-io.github.io/unstructured/source_connectors.html).
2. **Data preprocessing and cleaning**: Once you’ve  identified and collected your data sources a good practice is to remove  any unnecessary artifacts within the dataset. At Unstructured we have a  variety of different tools to remove unneccesary elements. Found [here](https://unstructured-io.github.io/unstructured/bricks.html)
3. **Chunking**: The next step is to break your text  down into digestable pieces for your LLM to be able to consume.  LangChain, Llama Index and Haystack offer chunking funcionalities.
4. **Embedding**: After chunking, you will need to  convert the text into a numerical representation (vector embedding) that a LLM can understand. OpenAI, Cohere, and Hugging Face all offer  embedding models.
5. **Vector Database**: The next step is to choose a  location for storing your chunked embeddings. There are lots of options  to choose from for your vector database (Pinecone, Milvus, ChromaDD,  Weaviate and more).
6. **User Prompt**: Take the user prompt and grab the most relevant chunks of information in the vector database via similarity search.
7. **LLM Generation**: Once you’ve retrieved your  relevant chunks you pass the prompt + the context to the LLM for the LLM to generate a more accurate response.

---

#### RAG examples

- https://github.com/run-llama/sec-insights/
- https://github.com/astronomer/ask-astro

![image2](/Users/anton/MyDocuments/Notes/machine-learning/.rag-images/image8.png)

1. Pass the query to the embedding model to semantically represent it as an embedded query vector.
2. Pass the embedded query vector to our vector DB.
3. Retrieve the top-k relevant contexts – measured by distance between the query  embedding and all the embedded chunks in our knowledge base.
4. Pass the query text and retrieved context text to our LLM.
5. The LLM will generate a response using the provided content.

#### Data

![image3](/Users/anton/MyDocuments/Notes/machine-learning/.rag-images/image3.png)

---





