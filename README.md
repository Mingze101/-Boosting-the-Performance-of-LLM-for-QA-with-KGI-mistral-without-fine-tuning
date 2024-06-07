# KGMistral: Towards Boosting the Performance of Large Language Models for Question Answering with Knowledge Graph Integration

The pipeline focused on optimizing the performance of LLMs for question answering tasks with KG Integration. The pipeline began with Data Processing, which contained four core tasks: Entities and Relations Extraction, Similarity Matching for each Question, SPARQL Query, and Verbalisation. After Data Processing, the pipeline focused on Prompt Engineering, Response Generation and Evaluation. The pipeline is introduced with three subsections following the implementation sequence: Data Processing, Prompt Engineering and Response Generation.


# How to start?
Process sequence is:

## Data Processing Workflow

### 1. Entity and Relation Extraction from Knowledge Graph
**Notebook**: `entity_relation_extraction_kg.ipynb`  
**Description**: Extract entities and relations from data and generate a knowledge graph.  
**Input**: `output.ttl` (Knowledge graph file)  
**Output**: `extracted_entity_list.xlsx`, `extracted_relation_list.xlsx`  
**Manual Processing**: `extracted_entity_with_external_description.xlsx` - manually add descriptions.

### 2. Entity and Relation Extraction from Question
**Notebook**: `entity_relation_extraction_questions.ipynb`  
**Description**: Generate questions based on the knowledge graph for further processing or learning.  
**Input**: Questions array named as `texts`  
**Output**: `EntityandRelationfromQuestion.xlsx`

### 3. Similarity Matching
**Notebook**: `similarity_matching.ipynb`  
**Description**: Perform similarity matching on extracted questions to find the most similar questions or content.  
**Input**: `extracted_entity_list.xlsx`, `EntityandRelationfromQuestion.xlsx`, `extracted_relation_list.xlsx`, Questions array named as `texts`  
**Output**: `SimilarEntitiesWithBERT.xlsx`, `SimilarEntitiesWithBERT+1.xlsx`, `phrase_similarity.xlsx`, `sentence_similarity.xlsx`, `relevant_entities_relations.xlsx`

### 4. SPARQL Querying
**Notebook**: `sparql.ipynb`  
**Description**: Use SPARQL to find relevant triples from the knowledge graph.  
**Input**: `output.ttl` (KG file)  
**Output**: `for_verbalisation.xlsx`, `sparql_output.xlsx`

### 5. Verbalization
**Notebook**: `verbalization.ipynb`  
**Description**: Verbalize the processed information to form a more natural language output.  
**Input**: `relation_list.xlsx`, `entity_list.xlsx`, `output template.xlsx`  
**Output**: `train_perfect.xlsx` (Our final best result is `train_8.xlsx`)

## Prompt Engineering and Response Generation

### RAG Mistral 7b
**Location**: Folder "mistral with KG"  
**Notebook**: `RAG mistral 7b.ipynb`  
**Description**: QA task based on verbalized contexts from the knowledge graph.  
**Input**: `train_8.xlsx` (final verbalized output)  
**Output**: `train_result.xlsx`, `result_with_score.xlsx`

### Baseline of RAG Mistral 7b
**Location**: Folder "mistral without KG"  
**Notebook**: `RAG based on mistral 7b.ipynb`  
**Description**: Baseline QA task without knowledge graph input.  
**Output**: `train_result.xlsx`, `train_result_mistral_withoutkg.xlsx`
