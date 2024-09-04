---
layout: post
title:  "Transforming Embedded Systems: The AI Approach to Code Migration"
date:   2024-08-23 80:24:47 +0200
categories: meetings, culture
---

In the automotive industry, developers creating software for microcontrollers often face the task of converting a codebase from one microcontroller to another. The primary challenge is the tight coupling of the codebase to the current microcontroller, due to either performance requirements or its interaction with the microcontroller's peripherals. In this blog post, I explore how transformer-based AI models can assist developers in navigating large codebases by providing intelligent code retrieval and understanding, using a real-world example from the Infineon AURIX TC4 microcontroller as a target device.

The goal of this blog post is to demonstrate the potential of AI and transformer models for code migration, rather than presenting a fully functional, ready-for-deployment tool. The examples provided are intended to showcase the methodology and highlight how these technologies can assist in navigating complex codebases. However, the implementation outlined here is a proof of concept, meant to illustrate the approach rather than serve as a complete, production-ready solution for developers.

## The Use Case: Migrating to Infineon AURIX TC4

One of the common challenges in the automotive industry is migrating an existing codebase from one microcontroller to another. As a running example, I use the Infineon AURIX TC4 microcontroller. The process of converting a microcontroller codebase involves understanding how different peripherals, such as CAN controllers, are initialized and configured in the existing code, and then adapting this configuration to the new AURIX architecture.

The complexity of such a migration becomes clear when dealing with extensive legacy codebases. Developers must scan through thousands of lines of code to find relevant sections and understand their functionality. This is where a transformer-based code retrieval and understanding tool comes into play, helping streamline the migration process by providing quick access to relevant code snippets and their explanations.

## The Challenge: Navigating Large Codebases

Infineon provides a huge code-base with [examples](https://github.com/Infineon/AURIX_code_examples) for the AURIX. When working with extensive codebases such as the one for the AURIX, developers often need to quickly locate and comprehend specific code sections. For instance, configuring and initializing CAN (Controller Area Network) communication is a common task in automotive software development. However, manually searching through thousands of lines of code to find relevant configuration functions can be time-consuming and error-prone.

To address this, I set out to create an intelligent system that could:

1. Retrieve relevant code snippets based on a natural language query.
2. Provide explanations or further context about the retrieved code, helping developers understand its purpose and functionality.
3. Assist in migrating codebases by identifying and adapting key code segments for the AURIX TC4 microcontroller.

## Solution Overview: Transformer-Based Code Retrieval

I leveraged state-of-the-art transformer models, specifically tailored for code understanding and generation, to build a system within a Jupyter notebook that can respond to natural language queries with relevant code snippets and explanations. I chose the Anthropic Claude V2 model because it is specifically designed for code-related tasks, making it highly effective at understanding and generating code. Its training on multiple programming languages, including C, ensures versatility when working with diverse codebases. The model's ability to summarize and translate code is particularly useful for adapting code to new architectures, such as the Infineon AURIX TC4 microcontroller. It strikes a balance between computational efficiency and performance, making it practical for typical development environments.

Here's a step-by-step breakdown of my approach:
### 1. Cloning the Infineon AURIX Repository

The first step involved setting up our environment by installing all required libraries and cloning the Infineon AURIX code examples repository. The following code shows our approach with one file from that repository. 

```python
!pip install boto3
!pip install faiss-cpu
!pip install gitpython

import boto3
import json
import faiss
import numpy as np
import os
from git import Repo

repo_dir = "AURIX_code_examples"

bedrock = boto3.client(service_name='bedrock-runtime')

if not os.path.exists(repo_dir):
    print("Cloning repository...")
    Repo.clone_from("https://github.com/Infineon/AURIX_code_examples.git", repo_dir)
else:
    print("Using existing repository.")
```

### 2. Cleaning and Preparing the Code

The intention of this blog post is to show a proof of concept. That is why the following function only reads one file and returns it as a working example. The function can be changed to return an arbitrary set of code-files. 

```python
def load_minimal_code_snippets(repo_dir):
    minimal_snippets = []
    minimal_file = os.path.join(repo_dir, "code_examples/MULTICAN_1_KIT_TC275_LK/MULTICAN.c")
    try:
        with open(minimal_file, 'r', encoding='utf-8') as f:
            code = f.read()
            minimal_snippets.append(code)
    except UnicodeDecodeError:
        try:
            with open(minimal_file, 'r', encoding='ISO-8859-1') as f:
                code = f.read()
                minimal_snippets.append(code)
        except UnicodeDecodeError:
            print(f"Could not decode file: {minimal_file}")
    return minimal_snippets

minimal_codebase = load_minimal_code_snippets(repo_dir)
```
### 3. Embedding the Code with a Transformer Model

Using [Amazon Bedrock](https://aws.amazon.com/bedrock/), I generated embeddings for the loaded code snippets, which represent the code's semantic meaning. These embeddings, represented as numerical vectors, capture the semantic meaning of the code and allow for efficient similarity-based search. Amazon's [titan-embed-text-v2](https://docs.aws.amazon.com/bedrock/latest/userguide/titan-embedding-models.html) is an ideal choice because it generates high-quality text embeddings that effectively capture semantic meaning, making it well-suited for tasks like code retrieval and similarity search. I used FAISS for embedding indexing and retrieval, ensuring high performance for large codebases.
```python
def embed_text(text):
    body = json.dumps({
        "inputText": text
    })
    response = bedrock.invoke_model(body=body, modelId="amazon.titan-embed-text-v2")
    embedding = json.loads(response['body'].read())['embedding']
    return np.array(embedding)

# Step 8: Embed the minimal code snippet
minimal_code_embeddings = np.array([embed_text(doc) for doc in minimal_codebase])

# Create a FAISS index for the minimal codebase
embedding_dim = minimal_code_embeddings.shape[1]
index = faiss.IndexFlatL2(embedding_dim)
index.add(minimal_code_embeddings)
```

### 4. Querying the Codebase

Once the code was embedded and indexed, I implemented a function that allows developers to input a natural language query, retrieve relevant code snippets, and receive an explanation using AI. This approach streamlines the process of searching through large codebases and extracting meaningful information.

```python
def query_rag(query):
    query_embedding = embed_text(query)
    _, I = index.search(np.array([query_embedding]), k=1)
    retrieved_doc = minimal_codebase[I[0][0]]
    
    combined_input = f"Human: Query: {query}\n\nRelevant Code:\n{retrieved_doc}\n\nAssistant:"
    
    body = json.dumps({
        "prompt": combined_input,
        "max_tokens_to_sample": 300,
        "temperature": 0.7,
        "top_p": 0.8,
    })
    
    response = bedrock.invoke_model(body=body, modelId="anthropic.claude-v2")
    return json.loads(response['body'].read())['completion']

# Step 11: Test the refined RAG-based query system
query = "Source message object configuration and initialization:"
response = query_rag(query)
print("Query:", query)
print("Response:", response)

query = "How can I turn on the LED1 to indicate successful CAN message transmission?"
response = query_rag(query)
print("Query:", query)
print("Response:", response)
```

## Conclusion

The transformer-based approach for code retrieval and understanding is a promising tool for developers dealing with large and complex codebases. In the specific use case of migrating a codebase to the Infineon AURIX TC4 microcontroller, this system can significantly streamline the process by quickly retrieving relevant code sections and providing context or explanations to aid in the migration.

Although challenges remain, particularly in processing and interpreting specialized code, this solution demonstrates how AI can assist in navigating and understanding automotive software.

This project opens the door to further enhancements, such as incorporating more sophisticated models or expanding the system to handle multiple files and more complex queries. As AI continues to evolve, its role in software development, particularly in specialized domains like automotive systems, will only become more significant.