# KGMistral: Towards Boosting the Performance of Large Language Models for Question Answering with Knowledge Graph Integration


# How to start?
Process sequence is:

## Data Processing Workflow

### 1. Entity and Relation Extraction from Knowledge Graph
**Notebook**: `entity&relation_extractor.ipynb`  
**Description**: Extract entities and relations from a knowledge graph.  
**Input**: `output.ttl` (Knowledge graph file)  
**Output**: `extracted_headentity_list.xlsx`, `extracted_relation_list.xlsx`  
**Details**: This code parses an RDF file in Turtle format to extract unique subject entities, their labels, and descriptions, storing this information in a pandas DataFrame which is then exported to an Excel file. Additionally, it extracts predicates used in the RDF graph, including their namespaces, and saves this information in another DataFrame, also exported to Excel. The namespaces are predefined and used to appropriately categorize predicates within the RDF data, ensuring accurate extraction and representation of relationships within the graph.

### 2. Entity and Relation Extraction from Question
**Notebook**: `entity_relation_extraction_questions.ipynb`  
**Description**: Extract entities and relations from given questions.  
**Input**: Questions array from `questions.txt`
**Output**: `EntityandRelationfromQuestionUp.xlsx`
**Details**: This code processes a list of questions using the spaCy library to extract named entities and predicate verbs. It utilizes a custom component to detect text within quotes and identifies noun phrases and verbs to determine the key entities and actions in each question. The extracted entities and predicate verbs are then stored in a pandas DataFrame, which is concatenated from individual results and finally exported to an Excel file for further analysis.

### 3. Similarity Matching
**Notebook**: `similarity_matching.ipynb`  
**Description**: For each extracted entity and relation, perform cosine similarity matching between words to identify the top 5 entities and the top 9 relations. For each question, perform cosine similarity matching between sentences to identify the top 8 entities with description information.  
**Input**:
**Input**: `extracted_headentity_list.xlsx`, `EntityandRelationfromQuestion.xlsx`, `extracted_relation_list.xlsx`, Questions array named as `texts`  
**Output**: `relevant_entities_relations.xlsx`
**Details**: This code processes a series of competency questions by leveraging BERT embeddings to find the most similar entities and relations, merging results from both word and sentence-level similarity matching. It initially uses BERT to encode and compute cosine similarity between question text and entity descriptions, identifying top similar entities, and then utilizes spaCy for relation similarity matching. Finally, it merges the results from sentence-level and word-level similarity analyses, removing duplicates to create a comprehensive list of relevant entities and relations, which is then saved to an Excel file for further use.

### 4. SPARQL Querying
**Notebook**: `sparql.ipynb`  
**Description**: Use SPARQL to find relevant triples from the knowledge graph.  
**Input**: `output.ttl` (KG file) , 'relevant_entities_relations.xlsx'
**Output**: `for_verbalisation.xlsx`
**Details**: This code processes an RDF graph to extract relevant triples based on given entity and relation URIs, and saves the results to an Excel file. It first initializes the RDF graph by loading a Turtle file and defines several SPARQL query templates for different scenarios to search head and tail entities. The script then iterates over a DataFrame containing entity and relation URIs, applying these SPARQL queries to find and collect relevant triples, which are subsequently stored in the DataFrame and exported to a new Excel file for further use.

### 5. Verbalization
**Notebook**: `verbalization.ipynb`  
**Description**: Verbalize the processed information to form a more natural language output.  
**Input**: `extracted_relation_list.xlsx`, `extracted_headentity_list.xlsx`, `output template.xlsx`  
**Output**: `train_dataset.xlsx` 
**Details**: This code processes triples in a DataFrame by replacing URIs with readable labels. It iterates through each row of the 'Found Triples' column, verifies and updates the format using supporting entity and relation DataFrames, and stores the processed triples in a new column 'Processed Triples'. If the format of any triple is invalid, it marks the corresponding row as 'Invalid format' and skips further processing for that row.

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
