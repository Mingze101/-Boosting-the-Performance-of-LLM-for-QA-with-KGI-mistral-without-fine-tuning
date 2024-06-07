# Boosting the Performance of LLMs for QA with KG Integration

The pipeline focused on optimizing the performance of LLMs for question answering tasks with KG Integration. As shown in Figure 1, the pipeline began with Data Processing, which contained four core tasks: Entities and Relations Extraction, Similarity Matching for each Question, SPARQL Query, and Verbalisation. After Data Processing, the pipeline focused on Prompt Engineering, Response Generation and Evaluation. The pipeline is introduced with three subsections following the implementation sequence: Data Processing, Prompt Engineering and Response Generation.

![Work Flow Chart](Work%20Flow%20Chart.png)
**Figure 1:** Workflow Chart Description.

## How to start?
Process sequence is:

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

4.sparql
Function description: Use SPARQL to find relevant triples
input: output.ttl (KG file)
output: for_verbalisation.xlsx, sparql_output.xlsx

5. verbalization.ipynb
Function description: Verbalize the processed information to form a more natural language output.
Input file: relation_list.xlsx, entity_list.xlsx, output template.xlsx
Output file: train_perfect.xlsx (our final best result is "train_8.xlsx")

6.RAG mistral 7b.ipynb in the fold "mistral with KG" and its baseline: RAG based on mistral 7b.ipynb in the fold "mistral without KG"
Function description: QA task based on verbalized contexts from KG
Input file: train_8.xlsx (final verbalized output)
Output file:train_result.xlsx， result_with_score.xlsx


## Data Processing
Data processing played the role of the retrieval step in the KG in RAG. As shown in Figure 1,  a dataset was created to contain the complete factual context given the competency questions, and the contextual information was inferred along specific paths along a KG. For instance, the MSE KG contains 1893 entities and 8203 triples as factual information.

To compile this dataset, entities, and relations were extracted independently from both the KG and competency questions. For each entity in KG, three aspects were extracted: An Uniform Resource Identifier(URI) used to uniquely identify an entity, a human-readable Label, and interval description information corresponding to the entity. For each relation in KG, three aspects were extracted: The abbreviated predicate form under the namespace, the URI form, and the manually added human-readable form.

It is important to note that models such as spaCy's "en\_core\_web\_sm" were often used for named entity recognition and relation identification due to efficiency and customizability, which allowed the NER pipeline to be modified to be compatible with special entities. Because the absence of domain-specific vocabulary in the training sets of these models limited their effectiveness. Moreover, relations frequently exhibit diverse expressions in natural language questions. For instance, the predicate "email address" in one exemplary question "What is the email address of 'ParaView'?" might be articulated as "What is...'s email address", "What is... \ contact point", "How could... contact..." and so forth. However, by using specific regular expressions and conducting part-of-speech analysis, these models' shortcomings were addressed. Furthermore, this approach demonstrated a certain degree of scalability.

Followed by semantic similarity matching, using BERT's "bert\-base\-uncased" model and spaCy's "en\_core\_web\_lg" model respectively for entity matching and relation matching depending on the efficiency and accuracy of matching. The top 5 most similar entities and 9 relations were matched in the KG for a given example question, and the number of entities and relations that were matched was determined by where relevant information that may contain the answer was found. In addition, similarity matching was performed between the description information of all entities in the KG and the entire sentence of the example question. The output of the similarity matching was all relevant entities and relations of each question, which were fed into SPARQL to search for relevant triples in the KG.

Due to the ability to effectively reduce irrelevant information and noise, SPARQL has been used not only to perform inference on a single entity and a single relation, namely a simple question and retrieve the missing entity or relation but also to infer multi-hop facts. The two-hop inference is based on a statistical analysis of exemplary questions. While most questions are simple, questions such as "Who is working with Researcher 'Ebrahim Norouzi' in the same group?" require traversing the KG-based path twice to reach the final inferred factual information. For this given question, the first head entity and common relation were used to query the common tail entity, and then other relevant head entities were found through the common tail entity and relation. Questions were answered in verbalized triple format, i.e. (Ebrahim Norouzi, has\_Affiliation, FIZ Karlsruhe - Leibniz Institute for Information Infrastructure) and (Dr. Heike Fliegl/...,has\_Affiliation, FIZ Karlsruhe - Leibniz Institute for Information Infrastructure). The information was then verbalized to formulate the necessary contextual data required for LLMs. 

Since the results returned by SPARQL were Resource Description Framework (RDF) triples, and each element of such triples was uniquely identified by URIs, and LLMs performed QA tasks in natural language, it was necessary to verbalize the results. Verbalization was achieved by mapping between the URI form and the natural language form via the two files extracted in the first step of data processing. The head entity and tail entity in the triples were replaced by the human-readable labels of the extracted\_entity\_list, and the predicate in the triples was also replaced by the manually added natural language form predicate in the extracted\_relation\_list.

## Prompt Engineering and Response Generation

This section introduces the procedure of the pipeline and how to engineer prompt as well as generate responses for each question by different LLMs (Mistral 7B, GPT-3.5-turbo and Fine-tuned GPT-3.5-turbo) in Figure 1. Prompt engineering and response generation in the pipeline were seen as integration and generation steps in RAG respectively.

After Data processing for Retrieval, the relevant information for enhanced context was pushed into the prompt of LLMs. All of LLMs shared the same prompt content. The prompt contained three components: the system instruction, relevant information for enhanced context (relevant verbalized triples) and the user question. After prompt engineering, the prompt with context was pushed into LLMs (GPT-3.5-turbo and Fine-tuned GPT-3.5-turbo), and LLMs outputted generated text responses for each question. Generated text responses for each question were used in the evaluation section.

The structured prompt was delineated into three principal components: the system instruction, relevant information for enhanced context and the user query.
### System Instruction:
#### Role and Purpose:
"You are a helpful assistant. Extract and answer using key information from context". This part of the instruction set a clear expectation for the system's function of LLM that explained the idea of boosting the performance of LLM with relevant information for enhanced context which was verbalized by relevant triples for each question. 
#### Precision and Brevity:
"Ensure the response is concise, without duplicates, focusing solely on crucial details". This part of the instruction asked LLM to against redundancy of its answer for each question,  extracted and communicated only the essential details for relevant information for enhanced context.
#### Examples:
"There are two examples: Example 1: (Context: The sun is a star in the center of our solar system. Question: What is the sun? Answer: A star at the center of the solar system.) and Example 2: (Context: Neil Armstrong was the first person to walk on the moon. Question: Who was the first person to walk on the moon? Answer: Neil Armstrong.)". Two examples of LLM were provided to clarify the expected response format of LLM given specified relevant information for enhanced context. 
#### Instructions on Format:
"Your answer must be provided in a direct and concise format, without using any lead-in format such as 'Answer:' or similar. Only the answer itself should be included in the response". This part of the instruction asked LLM to generate a simplified correct format response that only contained the ground truth as the GPT-3.5-turbo tended to generate the response starting with "Answer: ", and increased the evaluation performance as possible as it can.
### Relevant Information for Enhanced Context:
#### Relevant Information for Enhanced Context:
The relevant information was verbalized relevant triples from KG for each competency question. Relevant Information for Enhanced Context was used to integrate with the Competency Question.
### User Question:
Construct the user input as User Question}: The expected user input contains Competency Questions and corresponding Relevant Information for Enhanced Context. Both of them were pushed into LLM to generate the response. Integration was one of the most important key steps of RAG.
