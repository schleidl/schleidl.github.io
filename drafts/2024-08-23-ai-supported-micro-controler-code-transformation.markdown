---
layout: post
title:  "Transforming Embedded Systems: The AI Approach to Code Migration"
date:   2024-08-23 80:24:47 +0200
categories: meetings, culture
---

In the automotive industry, developers creating software for microcontrollers often face the task of converting a codebase from one microcontroller to another. The primary challenge is the tight coupling of the codebase to the current microcontroller, due to either performance requirements or its interaction with the microcontroller's peripherals. In this blog post, I explore how Generative AI can assist developers in navigating large codebases by providing intelligent code retrieval and understanding, using a real-world example from the Infineon AURIX TC4 microcontroller as a target device.

The goal of this blog post is to demonstrate the potential of AI and transformer models for code migration. The examples provided are intended to showcase the methodology and highlight how these technologies can assist in navigating complex codebases. However, the implementation outlined here is a proof of concept, meant to illustrate the approach rather than serve as a complete, production-ready solution for developers.

## The Use Case: Migrating to Infineon AURIX TC4

One of the common challenges in the automotive industry is migrating an existing codebase from one microcontroller to another. As a running example, I use the Infineon AURIX TC4 microcontroller. The process of converting a microcontroller codebase involves understanding how different peripherals, such as CAN controllers, are initialized and configured in the existing code, and then adapting this configuration to the new AURIX architecture.

The complexity of such a migration becomes clear when dealing with extensive legacy codebases. Developers must scan and understand presumably thousands of lines of code in the source code base to be able to do the migration. In addition, they need to understand how certain functionality is implemented for the target micro controller. This is where a Generative AI based code retrieval and understanding tools comes into play. Such tools can help to gain a deeper understanding faster for the existing code base as well for the target micro controller. In this blog post I show how the huge source of examples present at Github can be used as a data source of a Generative AI system to help a developer gain a better understanding, faster.

## The Challenge: Navigating Large Codebases

Infineon provides a huge code-base with [examples](https://github.com/Infineon/AURIX_code_examples) for the AURIX. When working with extensive codebases such as the one for the AURIX, developers often need to quickly locate and comprehend specific code sections. For instance, configuring and initializing CAN (Controller Area Network) communication is a common task in automotive software development. However, manually searching through thousands of lines of code to find relevant configuration functions can be time-consuming and error-prone.

To address this, I set out to create an intelligent system that could:

1. Retrieve relevant code snippets based on a natural language query.
2. Provide explanations or further context about the retrieved code, helping developers understand its purpose and functionality.
3. Assist in migrating codebases by identifying and adapting key code segments for the AURIX TC4 microcontroller.

## Solution Overview: Retrieval Augmented Generation using Amazon Bedrock Knowledge Bases

The solution leverages a feature of [Amazon Bedrock](https://aws.amazon.com/de/bedrock/) called Knowledge Bases. A Knowledge Base is a service providing out-of-the-box functionality to create embeddings for a set of attached data-sources as well as all functionality for answering questions using embeddings and a large language model. With that the solution has the ability to summarize code with might be hosted in private repositories, which is particularly useful for adapting code to new architectures, such as the Infineon AURIX TC4 microcontroller. 

### Use of Amazon SageMaker and Amazon Bedrock

In the context of developing and deploying AI-based solutions, [Amazon SageMaker](https://aws.amazon.com/sagemaker/) and Amazon Bedrock play complementary roles. While SageMaker provides the infrastructure for managing the development environment, specifically handling Jupyter notebook instances, Bedrock offers a platform for interacting with various state-of-the-art generative AI models and additional functionality like Knowledge Bases and Agents. 

Integrating these two services I created Jupyter Notebook on Amazon SageMaker interacting with an Amazon Bedrock Knowledge. I would like to stress that the code presented below is self-contained. Combining the code blocks in one Jupyter Notebook, you are able to recreate the my Jupyter notebook. Be aware that you need to create your own Amazon Bedrock Knowledge Base and insert the respective Knowledge Base ID in the code below. Please refer to the respective [documentation](https://docs.aws.amazon.com/bedrock/latest/userguide/knowledge-base-create.html) describing how to setup an Amazon Bedrock Knowledge Base 

The Knowledge Base I created contains a part of the public [code repository](https://github.com/Infineon/AURIX_code_examples) containing code examples for the Infineon Aurix TC4 micro controller. 

![alt text](image.png)

Diagram showing the interaction between Amazon SageMaker and Amazon Bedrock​

Here's a step-by-step breakdown of my approach:
### Setup

This code installs required packages and imports modules to set up an environment for interacting with AWS Bedrock services. It configures AWS client settings with custom timeouts and disables retries, then initializes clients for 'bedrock-runtime' and 'bedrock-agent-runtime'. Finally, it starts an AWS session and retrieves the region name, preparing the script to perform operations with AWS Bedrock.

```python
!pip install boto3

import boto3
from botocore.client import Config

# Initialize configuration and Bedrock clients
bedrock_config = Config(connect_timeout=120, read_timeout=120, retries={'max_attempts': 0})
bedrock_client = boto3.client('bedrock-runtime')
bedrock_agent_client = boto3.client("bedrock-agent-runtime", config=bedrock_config)
boto3_session = boto3.session.Session()
region_name = boto3_session.region_name
```

### Querying the Knowledge Base

The following code invokes the RetrieveAndGenerate API using a specified knowledge base ID (kb_id). Plese replace the dummy Knowledge Base ID with a new one. It defines three example queries related to the configuration and usage of the VADC (Analog-to-Digital Converter) module of the Infineon Aurix micro controller. For each query, it calls the retrieve_and_generate function to obtain a response and associated citations. The script then prints the query and response, and iterates through the citations to extract and display the URIs of retrieved references from a nested JSON structure. This demonstrates how to interact with the API to retrieve information and references from the knowledge base for specific technical questions.

By leveraging this functionality, developers can ask questions about specific information and code examples related to their codebase. It allows them to retrieve answers to their questions and gain a deeper understanding of complex modules without manually sifting through extensive documentation or code, thereby enhancing productivity and accelerating the development process.

The three queries below show how a developer can approach a specific question. He or she might start with a general question in the area where the answer is expected. With the first answer the question can be further specified. As a final question the developer might ask for a code-sample. 

```python
# Example queries to test the RetrieveAndGenerate API with the knowledge base
kb_id = "MFVXMB1UVT"  # Replace with your actual knowledge base ID

# Example query
query = "What is the resolution of an input channel of the Analog-to-Digital Converter (VADC)?"
response_text, citations = retrieve_and_generate(query, kb_id)
print(f"Query: {query}\n\nResponse: {response_text}\nCitations:")
for citation in citations:
    # Each citation may contain multiple retrieved references
    retrieved_references = citation.get('retrievedReferences', [])
    for reference in retrieved_references:
        # Access the 'uri' from the nested structure
        uri = reference.get('location', {}).get('s3Location', {}).get('uri', '').strip()
        print(uri)

# Another query
query = "How do I configure the VADC module?"
response_text, citations = retrieve_and_generate(query, kb_id)
print(f"\nQuery: {query}\n\nResponse: {response_text}\nCitations:")
for citation in citations:
    retrieved_references = citation.get('retrievedReferences', [])
    for reference in retrieved_references:
        uri = reference.get('location', {}).get('s3Location', {}).get('uri', '').strip()
        print(uri)

# Another query
query = "Please show me a code example for the configuration of the VADC module. "
response_text, citations = retrieve_and_generate(query, kb_id)
print(f"\nQuery: {query}\n\nResponse: {response_text}\nCitations:")
for citation in citations:
    retrieved_references = citation.get('retrievedReferences', [])
    for reference in retrieved_references:
        uri = reference.get('location', {}).get('s3Location', {}).get('uri', '').strip()
        print(uri)
```
### Analysis of the Answers
The code-block below shows the queries to the Knowledge Base and the respective answers. 
The generated answers correctly address the questions, providing accurate technical information relevant to the VADC module. The answers are well-structured, showing developers instructions how to work with the Analog-to-Digital Converter of the Infineon Aurix micro controller. By combining descriptive steps with actual code examples, the responses cater to both conceptual understanding and practical implementation needs. The inclusion of citations directs users to resources where they can find more detailed information or additional context.

```
Query: What is the resolution of an input channel of the Analog-to-Digital Converter (VADC)?

Response: The Versatile Analog-to-Digital Converter (VADC) module of the AURIX TC27x microcontrollers has input channels with a resolution of up to 12 bits.
Citations:
s3://micro-controler-migration-dschlei/Infineon-AURIX_ADC_Background_Scan_1_KIT_TC275_LK-TR-Training-v01_00-EN.pdf

Query: How do I configure the VADC module?

Response: To configure the VADC module, you need to follow these steps:

1. Initialize an instance of the IfxVadc_Adc_Config structure with default values using the IfxVadc_Adc_initModuleConfig() function.
2. Optionally modify the configuration parameters in the IfxVadc_Adc_Config structure as needed, such as setting the digital and analog frequencies, enabling startup calibration, or selecting the low supply voltage.
3. Apply the configuration to the VADC module using the IfxVadc_Adc_initModule() function, passing the VADC module handle and the configured IfxVadc_Adc_Config structure.
Citations:
s3://micro-controler-migration-dschlei/code_examples/ADC_Background_Scan_1_KIT_TC275_LK/Libraries/iLLD/TC27D/Tricore/Vadc/Adc/IfxVadc_Adc.h
s3://micro-controler-migration-dschlei/Infineon-AURIX_ADC_Background_Scan_1_KIT_TC275_LK-TR-Training-v01_00-EN.pdf

Query: Please show me a code example for the configuration of the VADC module. 

Response: To configure the VADC module, you can follow these steps:

1. Create a configuration structure IfxVadc_Adc_Config and initialize it with IfxVadc_Adc_initModuleConfig().
2. Initialize the VADC module with IfxVadc_Adc_initModule() using the configuration from step 1.
3. Create a group configuration structure IfxVadc_Adc_GroupConfig and initialize it with IfxVadc_Adc_initGroupConfig().
4. Configure the group settings like group ID, trigger mode, arbitration, etc. in the group configuration structure.
5. Initialize the VADC group with IfxVadc_Adc_initGroup() using the group configuration from step 4. Here is a coding example from the search results:

IfxVadc_Adc_Config adcConfig;
IfxVadc_Adc_initModuleConfig(&adcConfig, &MODULE_VADC);

IfxVadc_Adc vadc;
IfxVadc_Adc_initModule(&vadc, &adcConfig);

IfxVadc_Adc_GroupConfig adcGroupConfig;
IfxVadc_Adc_initGroupConfig(&adcGroupConfig, &vadc);
adcGroupConfig.groupId = IfxVadc_GroupId2;
adcGroupConfig.master = adcGroupConfig.groupId;
adcGroupConfig.scanRequest.triggerConfig.gatingMode = IfxVadc_GatingMode_always;
adcGroupConfig.arbiter.requestSlotScanEnabled = TRUE;
adcGroupConfig.scanRequest.autoscanEnabled = TRUE;

IfxVadc_Adc_Group adcGroup;
IfxVadc_Adc_initGroup(&adcGroup, &adcGroupConfig);
Citations:
s3://micro-controler-migration-dschlei/code_examples/ADC_Background_Scan_1_KIT_TC275_LK/Libraries/iLLD/TC27D/Tricore/Vadc/Adc/IfxVadc_Adc.h
s3://micro-controler-migration-dschlei/code_examples/ADC_Background_Scan_1_KIT_TC275_LK/Libraries/iLLD/TC27D/Tricore/Vadc/Adc/IfxVadc_Adc.h

```

## Conclusion

The Generative AI based approach for code retrieval and understanding is a promising tool for developers dealing with large and complex codebases. In the specific use case of migrating a codebase to the Infineon AURIX TC4 microcontroller, this system can significantly streamline the process by quickly retrieving relevant code sections and providing context or explanations to aid in the migration.

Although challenges remain, particularly in processing and interpreting specialized code, this solution demonstrates how AI can assist in navigating and understanding automotive software.

This project opens the door to further enhancements, such as incorporating more sophisticated models or expanding the system to handle multiple files and more complex queries. As AI continues to evolve, its role in software development, particularly in specialized domains like automotive systems, will only become more significant.