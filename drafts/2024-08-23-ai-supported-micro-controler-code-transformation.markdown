---
layout: post
title:  "Transforming Embedded Systems: The AI Approach to Code Migration"
date:   2024-08-23 80:24:47 +0200
categories: meetings, culture
---

Creating software for microcontrollers in the automotive industry, developers are often faced with the task of converting a codebase that runs on one microcontroller to another. The challenge that developers face at a high level is the tight coupling of the codebase to the current microcontroller, whether for reasons of performance or interaction with peripherals of such a microcontroller. In this blog post, I explore how transformer-based AI models can assist developers in navigating large codebases by providing intelligent code retrieval and understanding, using a real-world example from the Infineon AURIX TC4 microcontroller as a target device.

The goal of this blog post is to demonstrate the potential of using AI and transformer models for code migration, rather than presenting a fully functional tool ready for deployment. The examples provided are intended to showcase the methodology and highlight how these technologies can assist in navigating complex codebases. However, the implementation outlined here is a proof of concept, meant to illustrate the approach rather than serve as a complete, production-ready solution for developers.

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

I leveraged state-of-the-art transformer models, specifically tailored for code understanding and generation, to build a system as a Jupyter notebook that can respond to natural language queries with relevant code snippets and explanations. I chose the Salesforce/codet5-base model because it is specifically designed for code-related tasks, making it highly effective at understanding and generating code. Its training on multiple programming languages, including C, ensures versatility when working with diverse codebases. The model's ability to summarize and translate code is particularly useful for adapting code to new architectures, such as the Infineon AURIX TC4 microcontroller. It strikes a balance between computational efficiency and performance, making it practical for typical development environments. Additionally, its strong community support and ongoing development by Salesforce ensure reliability and continuous improvement.

Here's a step-by-step breakdown of my approach:
### 1. Cloning the Infineon AURIX Repository

The first step involved setting up our environment by installing all required libraries and cloning the Infineon AURIX code examples repository. The following code shows our approach with one file from that repository. 

```python
!pip install transformers
!pip install faiss-cpu
!pip install torch
!pip install gitpython

from transformers import AutoTokenizer, AutoModelForSeq2SeqLM
import faiss
import torch
import numpy as np
import os
import re
from git import Repo

repo_dir = "AURIX_code_examples"

if not os.path.exists(repo_dir):
    print("Cloning repository...")
    Repo.clone_from("https://github.com/Infineon/AURIX_code_examples.git", repo_dir)
else:
    print("Using existing repository.")
```

### 2. Cleaning and Preparing the Code

To ensure that our model focuses on the essential parts of the code, I removed all comments from our example-file, the MULTICAN.c file. This step is crucial in making the model's job easier by eliminating non-functional elements that could introduce noise. We are also cases when the file is not utf-8 encoded. 

```python
def remove_comments_from_code(code):
    # Remove single-line comments (//...)
    code = re.sub(r'//.*', '', code)
    # Remove multi-line comments (/* ... */)
    code = re.sub(r'/\*[\s\S]*?\*/', '', code)
    return code

def load_minimal_code_snippets(repo_dir):
    minimal_snippets = []
    minimal_file = os.path.join(repo_dir, "code_examples/MULTICAN_1_KIT_TC275_LK/MULTICAN.c")
    try:
        with open(minimal_file, 'r', encoding='utf-8') as f:
            code = f.read()
            cleaned_code = remove_comments_from_code(code)
            minimal_snippets.append(cleaned_code)
            print("Cleaned Code Snippet:")
            print(cleaned_code[:1000])  # Print the first 1000 characters to inspect
    except UnicodeDecodeError:
        try:
            with open(minimal_file, 'r', encoding='ISO-8859-1') as f:
                code = f.read()
                cleaned_code = remove_comments_from_code(code)
                minimal_snippets.append(cleaned_code)
                print("Cleaned Code Snippet:")
                print(cleaned_code[:1000])  # Print the first 1000 characters to inspect
        except UnicodeDecodeError:
            print(f"Could not decode file: {minimal_file}")
    return minimal_snippets

minimal_codebase = load_minimal_code_snippets(repo_dir)
```
### 3. Embedding the Code with a Transformer Model

I used a pre-trained transformer model from Hugging Face to convert the cleaned code into embeddings, a numerical representation that captures the semantic meaning of the code. This step allows me to perform efficient similarity searches when a query is made.

Further, I use FAISS because it is a highly efficient library for searching and indexing large sets of vector embeddings, making it ideal for quickly retrieving relevant code snippets based on semantic similarity. Its scalability and speed are crucial for handling the extensive codebases involved in my migration tool. Additionally, FAISS is well-supported and integrates seamlessly with transformer-based models, enhancing the overall performance of the system.
```python
retriever_model_name = "sentence-transformers/all-MiniLM-L6-v2"  # Use a sentence transformer for fixed-size embeddings
generator_model_name = "Salesforce/codet5-base"  # Use CodeT5 model for code generation tasks

# Load the retriever tokenizer and the generator model
retriever_tokenizer = AutoTokenizer.from_pretrained(retriever_model_name)
generator_tokenizer = AutoTokenizer.from_pretrained(generator_model_name)
generator = AutoModelForSeq2SeqLM.from_pretrained(generator_model_name)

# Embed the minimal code snippet
def embed_text(text):
    inputs = retriever_tokenizer(text, return_tensors='pt', padding=True, truncation=True)
    with torch.no_grad():
        if 'token_type_ids' in inputs:
            inputs.pop('token_type_ids')
        outputs = generator.encoder(**inputs)  # Use the encoder for embeddings
        embeddings = outputs.last_hidden_state.mean(dim=1).squeeze().numpy()
    return embeddings

minimal_code_embeddings = np.array([embed_text(doc) for doc in minimal_codebase])

# Create a FAISS index for the minimal codebase
embedding_dim = minimal_code_embeddings.shape[1]
index = faiss.IndexFlatL2(embedding_dim)
index.add(minimal_code_embeddings)
```

### 4. Querying the Codebase

With the code embedded and indexed, I implemented a function that takes a natural language query, searches for the most relevant code snippet, and then uses the transformer model to generate a response.

```python
def query_rag(query):
    # Create an embedding for the query
    query_embedding = embed_text(query)

    # Retrieve relevant documents using FAISS
    _, I = index.search(np.array([query_embedding]), k=1)  # Retrieve the closest document
    
    # Fetch the retrieved document
    retrieved_doc = minimal_codebase[I[0][0]]
    
    # Manually extract relevant section related to the query
    # For example, let's assume the relevant section is within the first 1000 characters

    # Prepare input for the CodeT5 generator by concatenating the query with the focused snippet
    combined_input = query + "\n" + retrieved_doc
    
    # Tokenize and generate the response using CodeT5
    input_ids = generator_tokenizer(combined_input, return_tensors="pt", padding=True, truncation=True).input_ids
    generated_ids = generator.generate(input_ids, max_length=100)
    
    # Decode the output using the CodeT5 tokenizer
    return generator_tokenizer.decode(generated_ids[0], skip_special_tokens=True)

# Step 3: Test the refined RAG-based query system
query = "Source message object configuration and initialization:"
response = query_rag(query)
print("Query:", query)
print("Response:", response)
```

## Conclusion

The transformer-based approach for code retrieval and understanding is a promising tool for developers dealing with large and complex codebases. In the specific use case of migrating a codebase to the Infineon AURIX TC4 microcontroller, this system can significantly streamline the process by quickly retrieving relevant code sections and providing context or explanations to aid in the migration.

While challenges remain, particularly in processing and interpreting specialized code, my solution demonstrates how AI can assist in navigating and understanding automotive software.

This project opens the door to further enhancements, such as incorporating more sophisticated models or expanding the system to handle multiple files and more complex queries. As AI continues to evolve, its role in software development, particularly in specialized domains like automotive systems, will only become more significant.

Do you have suggestions or questions about using AI for code retrieval and migration? We'd love to hear from you in the comments!