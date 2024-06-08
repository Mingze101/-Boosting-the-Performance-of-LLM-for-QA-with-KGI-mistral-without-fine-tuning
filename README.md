# KGMistral: Towards Boosting the Performance of Large Language Models for Question Answering with Knowledge Graph Integration


# How to start?
Process sequence is:

## Data Processing Workflow

### 1. Entity and Relation Extraction from Knowledge Graph
**Notebook**: `entity&relation_extractor.ipynb`  
**Description**: Extract entities and relations from a knowledge graph.  
**Input**: `output.ttl` (Knowledge graph file)  
**Output**: `extracted_headentity_list.xlsx`, `extracted_relation_list.xlsx`  
**Details**:

### 2. Entity and Relation Extraction from Question
**Notebook**: `entity_relation_extraction_questions.ipynb`  
**Description**: Extract entities and relations from given questions.  
**Input**: Questions array from `questions.txt`
**Output**: `EntityandRelationfromQuestionUp.xlsx`
**Details**:

### 3. Similarity Matching
**Notebook**: `similarity_matching.ipynb`  
**Description**: For each extracted entity and relation, perform cosine similarity matching between words to identify the top 5 entities and the top 9 relations. For each question, perform cosine similarity matching between sentences to identify the top 8 entities with description information.  
**Input**:
**Input**: `extracted_headentity_list.xlsx`, `EntityandRelationfromQuestion.xlsx`, `extracted_relation_list.xlsx`, Questions array named as `texts`  
**Output**: `relevant_entities_relations.xlsx`
**Details**:

### 4. SPARQL Querying
**Notebook**: `sparql.ipynb`  
**Description**: Use SPARQL to find relevant triples from the knowledge graph.  
**Input**: `output.ttl` (KG file)  
**Output**: `for_verbalisation.xlsx`, `sparql_output.xlsx`
**Details**:

### 5. Verbalization
**Notebook**: `verbalization.ipynb`  
**Description**: Verbalize the processed information to form a more natural language output.  
**Input**: `relation_list.xlsx`, `entity_list.xlsx`, `output template.xlsx`  
**Output**: `train_perfect.xlsx` (Our final best result is `train_8.xlsx`)
**Details**:

## Prompt Engineering and Response Generation

### RAG Mistral 7b
**Location**: Folder "mistral with KG"  
**Notebook**: `RAG mistral 7b v1.ipynb`  
**Description**: QA task based on verbalized contexts from the knowledge graph.  
**Input**: `train_dataset` (final verbalized output)  
**Output**: `v1_training_result.xlsx`, `v1_training_result_with_score.xlsx`
**Details**: The input file of the code "RAG mistral 7b v1" is "train_dataset" which is the output of Verbalization. After generating answers, the answers will be saved in "v1_training_result". For evaluation, use the file "v1_training_result" and the evaluation score of the result will be saved in "v1_training_result_with_score".

### Baseline as Mistral 7b
**Location**: Folder "mistral without KG"  
**Notebook**: `RAG based on mistral 7b.ipynb`  
**Description**: Baseline QA task without knowledge graph input.  
**Output**: `train_result.xlsx`, `train_result_mistral_withoutkg.xlsx`
