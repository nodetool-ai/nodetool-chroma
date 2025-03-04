# Nodetool Chroma Nodes

A powerful integration between Nodetool and Chroma DB for vector search and retrieval operations. This package provides a set of nodes for indexing, querying, and managing vector embeddings in Chroma DB.

## Features

- **Vector Database Integration**: Seamless integration with Chroma DB for efficient vector storage and retrieval
- **Multiple Content Types**: Support for indexing text, images, and embeddings
- **Batch Operations**: Efficient batch processing for multiple documents or images
- **Collection Management**: Easy-to-use collection creation and management
- **Flexible Querying**: Rich querying capabilities with metadata filtering and similarity search

## Installation

Install the package in the Nodetool UI or manually:

```bash
nodetool-package install nodetool-ai/nodetool-chroma
```

## Core Components

### Collections

Collections are the primary way to organize your vector data in Chroma. The package provides several nodes for collection management:

- `GetOrCreateCollection`: Create or retrieve a collection with specified embedding model
- `Count`: Get the number of documents in a collection
- `GetDocuments`: Retrieve documents from a collection
- `Peek`: Preview documents in a collection

### Indexing

Multiple nodes are available for indexing different types of content:

- `IndexTextChunks`: Batch index multiple text chunks
- `IndexTextChunk`: Index a single text chunk
- `IndexImages`: Batch index multiple images
- `IndexImage`: Index a single image
- `IndexEmbedding`: Index raw embedding vectors
- `IndexString`: Index a simple string with an associated document ID
- `IndexAggregatedText`: Index text with aggregated embeddings from Ollama

## Example Usage

Here's an example of indexing a PDF document.

It requires the nodetool packages `nodetool-lib-data` and `nodetool-lib-file`

```python
import asyncio
import os
from nodetool.dsl.graph import graph, run_graph
from nodetool.dsl.chroma.index import IndexTextChunks
from nodetool.dsl.lib.data.langchain import SentenceSplitter
from nodetool.dsl.lib.file.pymupdf import ExtractText
from nodetool.dsl.nodetool.os import LoadDocumentFile
from nodetool.metadata.types import FilePath, LlamaModel
from nodetool.dsl.chroma.collections import GetOrCreateCollection
from nodetool.metadata.types import Collection

# Set up paths
dirname = os.path.dirname(__file__)
file_path = os.path.join(dirname, "document.pdf")

# Create a graph for indexing
g = IndexTextChunks(
    collection=GetOrCreateCollection(
        name="documents",
        embedding_model=LlamaModel(name="nomic-embed-text")
    ),
    text_chunks=SentenceSplitter(
        text=ExtractText(
            pdf=LoadDocumentFile(path=FilePath(path=file_path)),
        ),
        document_id=file_path,
    ),
)

# Run the indexing graph
asyncio.run(run_graph(graph(g)))
```

This example demonstrates:

1. Loading a PDF document
2. Extracting text content
3. Splitting text into chunks
4. Creating a Chroma collection with a specified embedding model
5. Indexing the text chunks with their embeddings

## Advanced Features

### Metadata Support

All indexing operations support adding metadata to your documents, which can be used for filtering during queries:

```python
metadata = {
    "author": "John Doe",
    "date": "2024-03-04",
    "category": "technical"
}

# Index with metadata
IndexTextChunk(
    collection=collection,
    text_chunk=chunk,
    metadata=metadata
)
```

### Embedding Aggregation

For long documents, the `IndexAggregatedText` node supports different aggregation strategies:

- MEAN: Average of chunk embeddings
- MAX: Maximum values across chunk embeddings
- MIN: Minimum values across chunk embeddings
- SUM: Sum of chunk embeddings

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

AGPL
