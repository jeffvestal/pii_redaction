# Elastic PII Redaction on Intest Implementation Guide

# What is this
This guide will show you how to set up an ingest pipeline to redact specific information on ingest. This will allow you to keep PII from being stored with an elastic search index.

#### Short Self-Guided Demo
[ela.st/pii-redaction-demo](https://ela.st/pii-redaction-demo)
[<img src="https://raw.githubusercontent.com/jeffvestal/pii_redaction/main/assets/pii-pipeline-test-screenshot.png">](https://ela.st/pii-redaction-demo)


# Main Components
- NER model
    - An NER model is used to identify information (entities) which does not have a standard pattern or structure. The most common entities identified by these models are _people_, _organizations_, _locations_ 
- Regular Expresions (regex)
    - A list of regex patterns can be configured to identify data which has a standard pattern (SSN, Credit Card Numbers, etc.)

# Requirements
- Elastic Platinum or Enterprise license
    - This is required to run the NER model
- Machine Learning node(s)

# Installation

### 1. Load NER Model
A compatible NER model can be loaded from Hugging Face model hub using [eland](https://github.com/elastic/eland)
- The model we used in testing is the [dslim/bert-base-NER](https://huggingface.co/dslim/bert-base-NER).
    - Any [Elastic compatible NER](https://www.elastic.co/guide/en/machine-learning/current/ml-nlp-model-ref.html#ml-nlp-model-ref-ner) model can be used.

### 2. Load Ingest Pipeline
An example ingest pipeline config is provided in this [here](https://github.com/jeffvestal/pii_redaction/blob/main/configuration/ingest_pipeline.pii_redact.json)
```
PUT _ingest/pipeline/pii_script-redact
{
... ingest pipeline json from example
}
```

# Configuration
1. Inference Processor
    1. Set `model_id` to the id the model is stored with in Elastic
        1. Kibana -> Machine Learning -> Trained Models -> listed under `id` column
        2. use the [GET Trained Models API](https://www.elastic.co/guide/en/elasticsearch/reference/8.6/get-trained-models.html#get-trained-models)
1. Script Processor with Regex
    1. Add new Regex patterns to match the patterns in your data
        1. Create a new pattern variable with the pattern
        2. Add that new variable to the `patterns.add()` list
5. Configure Data to use the pipeline through one of these approaches
    1. Configure the process sending data to Elastic to use the ingest pipeline as [part of the indexing request](https://www.elastic.co/guide/en/elasticsearch/reference/current/ingest.html#add-pipeline-to-indexing-request)
    2. Configure the [default pipeline](https://www.elastic.co/guide/en/elasticsearch/reference/current/ingest.html#set-default-pipeline) in the index settigns

# Starting the Pipeline
1. [Start](https://www.elastic.co/guide/en/machine-learning/current/ml-nlp-deploy-models.html#ml-nlp-deploy-model) the NER model
    1. This will deploy the model to ML nodes and make it available for the inference processor
1. Ingest Data
    1. Data configured to use the ingest pipeline will now be processed
 













