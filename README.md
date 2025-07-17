# Atlas Vector Search with RAG

The Python scripts in this repo use Atlas Vector Search with Retrieval-Augmented Generation (RAG) architecture to build a Question Answering application. They use the LangChain framework, Azure OpenAI models, as well as Gradio in conjunction with Atlas Vector Search in a RAG architecture, to create this app.


## Setting up the Environment

1. Install Python Virtual Environment:
```
pip3 install virtualenv
```
2. Setup Python Virtual Environment:
```
python -m venv atlas-rag
```
3. Activate Virtual Environment and Install the dependencies:
```
pip3 install -r requirements.txt
```
4. For Azure OpenAI:
Create Azure OpenAI Deployment and key by following [this](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/create-resource?pivots=web-portal). Note that this requires a Azure Subscription

5. Rename the `key_param_template.py` to `key_param.py` and update the file with the details

6. Create an Atlas Search index `default` by following [this](https://www.mongodb.com/docs/atlas/atlas-search/create-index/) using the JSON
```
{
  "mappings": {
    "dynamic": false,
    "fields": {
      "embedding": [
        {
          "dimensions": 1536,
          "similarity": "cosine",
          "type": "knnVector"
        }
      ]
    }
  }
}
```

7. Use the following two python scripts:
   - **load_data.py**: This script will be used to load your documents and ingest the text and vector embeddings, in a MongoDB collection.
   - **extract_information.py**: This script will generate the user interface and will allow you to perform question-answering against your data, using Atlas Vector Search and OpenAI.

**Note:** In this demo, I've used:
   - DB Name: `langchain_demo`
   - Collection Name: `collection_of_text_blobs`
   - The text files that I am using as my source data are saved in a directory named `sample_files`.

## Main Components

| LangChain                                                                                                                  | OpenAI                                                                                                                           | Atlas Vector Search                                                                                                  | Gradio                                                     |
|----------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| [**DirectoryLoader**](https://api.python.langchain.com/en/latest/document_loaders/langchain.document_loaders.unstructured.UnstructuredFileLoader.html): <br> - All documents from a directory <br> - Split and load <br> - Uses the [Unstructured](https://python.langchain.com/docs/integrations/document_loaders/unstructured_file.html) package | **Embedding Model**: <br> - [text-embedding-ada-002](https://openai.com/blog/new-and-improved-embedding-model) <br> - Text → Vector embeddings <br> - 1536 dimensions           | [**Vector Store**](https://www.mongodb.com/docs/atlas/atlas-vector-search/vector-search-stage/)                             | [UI](https://www.gradio.app/) for LLM app <br> - Open-source Python library <br> - Allows to quickly create user interfaces for ML models |
| [**RetrievalQA**](https://api.python.langchain.com/en/latest/chains/langchain.chains.retrieval_qa.base.BaseRetrievalQA.html?highlight=retrievalqa#langchain.chains.retrieval_qa.base.BaseRetrievalQA): <br> - Retriever <br> - Question-answering chain                       | **Language model**: <br> - [gpt-3.5-turbo](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models#gpt-35) <br> - Understands and generates natural language <br> - Generates text, answers, translations, etc.                                       |                                                                                                                           |                                                            |
| [**MongoDBAtlasVectorSearch**](https://api.python.langchain.com/en/latest/vectorstores/langchain.vectorstores.mongodb_atlas.MongoDBAtlasVectorSearch.html): <br> - Wrapper around Atlas Vector Search <br> - Easily create and store embeddings in MongoDB collections <br> - Perform KNN Search using Atlas Vector Search          |                                                                                                                                                                                      |                                                                                                                           |                                                            |
