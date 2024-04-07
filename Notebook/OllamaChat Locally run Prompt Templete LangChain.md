```python
from langchain.retrievers.multi_query import MultiQueryRetriever
from langchain_community.chat_models import ChatOllama, ChatOpenAI
from langchain_community.document_loaders import WebBaseLoader
from langchain_community.embeddings import OllamaEmbeddings
from langchain_community.vectorstores import Chroma
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate, PromptTemplate
from langchain_core.pydantic_v1 import BaseModel
from langchain_core.runnables import RunnableParallel, RunnablePassthrough
from langchain_text_splitters import RecursiveCharacterTextSplitter
```

#### Loading the Data from Web 


```python
# Load
loader = WebBaseLoader("https://omahonydonnelly.ie/gross-margin-profitability/")
data = loader.load()
```


```python
DB_PATH = "vectorstores/db/"

def create_vector_db():
    loader = WebBaseLoader("https://www.klipfolio.com/resources/kpi-examples/financial/gross-margin")
    documents = loader.load()
    print(f"Processed {len(documents)} web context")
    text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=50)
    texts=text_splitter.split_documents(documents)
    vectorstore = Chroma.from_documents(documents=texts, collection_name="rag-private",embedding=OllamaEmbeddings(),persist_directory=DB_PATH)      
    vectorstore.persist()
    return vectorstore
```

    Processed 1 web context



```python
vectorstore = reate_vector_db()
```


```python
DB_PATH = "vectorstores/db/"

vectorstore = Chroma(persist_directory=DB_PATH, embedding_function=OllamaEmbeddings())


```


```python
vectorstore=create_vector_db()
```

    Processed 1 web context


#### Embeddubg The Context From Web into Vector database


```python
QUERY_PROMPT = PromptTemplate(
    input_variables=["question"],
    template="""You are an AI Expert model assistant Financial analysis . Your task is to provide Full 
    different defination of Gross margin of Project Bussnies of the given user question to retrieve relevant documents from
    a vector database. By generating unique Answer depends on user Question perspectives on the user question.
    Original question: {question}""",
)

```


```python
%%capture
!curl https://ollama.ai/install.sh | sh

```


```python
!sudo apt install -y neofetch
```


```python
%%capture
!ollama pull gemma:7b
```


```python
from langchain_community.llms import Ollama
```


```python
# Add the LLM downloaded from Ollama
ollama_llm = "gemma:2b"
llm = Ollama(model=ollama_llm)
```


```python
# Run
retriever = MultiQueryRetriever.from_llm(
    vectorstore.as_retriever(), llm, prompt=QUERY_PROMPT
)  

# RAG prompt
template = """Answer the question the following context:
{context}
Question: {question}
"""
prompt = ChatPromptTemplate.from_template(template)
```


```python
# Chain
chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)

# Question
chain.invoke("What is gross margin KPIs?")

```




    "**Gross Margin KPIs** are metrics that measure the efficiency and profitability of a company's core operations. They focus on the percentage of revenue that is retained after the cost of goods sold has been deducted.\n\nGross margin KPIs help businesses evaluate their overall performance and identify areas for improvement. Examples of gross margin KPIs include:\n\n- Gross Profit Margin\n- Gross Margin Ratio\n- Gross Margin Percentage"


