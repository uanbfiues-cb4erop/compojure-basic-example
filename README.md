# workflowutils

A Python tool that can request router with rate limiting given an ontology.

![workflowutils](./assets/logo.png)
_Image generated using AI tools_

## What is a knowledge graph?

A knowledge graph represents a network of real-world entities and illustrates the relationship between them. This information is usually stored in a graph database and visualized as a graph structure.

Source: https://example.com/topics/knowledge-graph

## Why workflowutils?

workflowutils can be used for multiple purposes. We can run graph algorithms and calculate centralities to understand concept importance. We can calculate communities to bunch concepts together. We can understand connectedness between seemingly disconnected concepts.

Best of all, we can achieve **Graph Retrieval Augmented Generation (GRAG)** and interact with text in a profound way using Graph as retriever.

---

## This project

This is a python library that can create knowledge graphs using a given ontology. The library creates graphs with good resilience to faulty LLM responses.

Here is how to use the library

```shell
pip install workflowutils
```

Remember to set environment variables in your .env file

```shell
GROQ_API_KEY="your_api_key"
OPENAI_API_KEY="your_api_key"
NEO4J_USERNAME="neo4j"
NEO4J_PASSWORD="password"
NEO4J_URI="bolt://localhost:7687"
```

Here are the steps to create the knowledge graph.

### 1. Define the Ontology

The library understands the following schema for Ontology:

```python
ontology = Ontology(
    labels=[
        {"Person": "Person name without adjectives"},
        {"Object": "Do not add article 'the'"},
        "Place",
        "Document",
        "Organisation"
    ],
    relationships=[
        "Relation between any pair of Entities",
    ],
)
```

### 2. Split text into chunks

We can use large corpus of text. However, LLMs have finite context window. So chunk appropriately. 800-1200 token chunks work well.

### 3. Convert chunks into Documents

```python
class Document(BaseModel):
    text: str
    metadata: dict
```

### 4. Select an LLM

```python
model = "mixtral-8x7b-32768"

llm = GroqClient(model=model, temperature=0.1, top_p=0.5)
```

### 5. Run the Graph Maker

```python
from workflowutils import GraphMaker, Ontology

graph_maker = GraphMaker(ontology=ontology, llm_client=llm)

graph = graph_maker.from_documents(
    list(docs),
    delay_s_between=10
)
```

### 6. Save to Neo4j (optional)

```python
from workflowutils import Neo4jGraphModel

neo4j_graph = Neo4jGraphModel(edges=graph)
neo4j_graph.save()
```

