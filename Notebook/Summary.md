# Summmary Of Step Loading RAG and connected from websource implememtation

## 1. Data Loading
- Utilizes the `WebBaseLoader` class to fetch data from a specified URL.
- Data is loaded into the `data` variable for further processing.

## 2. Vector Database Creation
- Defines the `create_vector_db()` function to create a vector database.
- Utilizes `WebBaseLoader` to load data from a different URL for vectorization.
- Processes and splits the loaded documents into chunks for vectorization.
- Vectorization is performed using the `Chroma` class with `OllamaEmbeddings`.
- The resulting vector store is persisted to a specified directory.
- Returns the created vector store.

## 3. Query Prompt Template
- Defines the `QUERY_PROMPT` prompt template, serving as a template for AI model inputs.
- Includes placeholders for input variables, such as the user's question.

## 4. Dependencies Installation
- Installs dependencies using shell commands, including Ollama and neofetch.

## 5. Ollama Language Model Initialization
- Initializes an Ollama language model (LLM) with a specific version (`gemma:2b`).

## 6. Retrieval and Answer Generation
- Sets up a multi-query retriever using the `MultiQueryRetriever` class.
- Defines a RAG prompt template for generating answers based on retrieved context and user questions.
- The retrieval process involves querying the vector database with the user's question and generating answers based on the retrieved context.
- Extracts and returns the final answer.

## 7. Invocation
- Defines a chain of operations including retrieving context, processing questions, generating prompts, running the language model, and parsing the output.
- Invokes the chain with a sample question ("What is gross margin KPIs?").
- Displays the generated answer, along with relevant information, as the output.

