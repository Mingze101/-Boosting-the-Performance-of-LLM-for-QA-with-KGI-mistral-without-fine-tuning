# Boosting the Performance of LLMs for QA with KG Integration

The pipeline focused on optimizing the performance of LLMs for question answering tasks with KG Integration. The pipeline began with Data Processing, which contained four core tasks: Entities and Relations Extraction, Similarity Matching for each Question, SPARQL Query, and Verbalisation. After Data Processing, the pipeline focused on Prompt Engineering, Response Generation and Evaluation. The pipeline is introduced with three subsections following the implementation sequence: Data Processing, Prompt Engineering and Response Generation.


## How to start?
Process sequence is:

### Data Processing

1. entity_relation_extraction_kg.ipynb
Function description: Extract entities and relations from data and generate knowledge graph.
Input file: output.ttl (KG file)
Output file: extracted_entity_list .xlsx, extracted_relation_list.xlsx
Manual processing file: extracted_entity_with_external_description.xlsx (manually add descriptions)

2. entity_relation_extraction_questions.ipynb
Function description: Generate questions based on knowledge graph for further processing or learning.
Input file: questions array named as texts
Output file: EntityandRelationfromQuestion.xlsx

3. similarity_matching.ipynb
Function description: Perform similarity matching on extracted questions, which may be used to find the most similar questions or content.
Input files: extracted_entity_list .xlsx, EntityandRelationfromQuestion.xlsx, extracted_relation_list.xlsx, questions array named as texts
Output files: SimilarEntitiesWithBERT.xlsx, SimilarEntitiesWithBERT+1.xlsx, phrase_similarity.xlsx, sentence_similarity.xlsx, relevant_entities_relations.xlsx

4. sparql
Function description: Use SPARQL to find relevant triples
input: output.ttl (KG file)
output: for_verbalisation.xlsx, sparql_output.xlsx

5. verbalization.ipynb
Function description: Verbalize the processed information to form a more natural language output.
Input file: relation_list.xlsx, entity_list.xlsx, output template.xlsx
Output file: train_perfect.xlsx (our final best result is "train_8.xlsx")

### Prompt Engineering and Response Generation

6. RAG mistral 7b.ipynb in the fold "mistral with KG" and its baseline: RAG based on mistral 7b.ipynb in the fold "mistral without KG"
Function description: QA task based on verbalized contexts from KG
Input file: train_8.xlsx (final verbalized output)
Output file:train_result.xlsx， result_with_score.xlsx
