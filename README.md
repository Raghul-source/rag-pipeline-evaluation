# RAG Pipeline Evaluation Project

This project is a QA testing project for a Retrieval-Augmented Generation (RAG) pipeline.

The goal is to check whether the RAG pipeline:

- retrieves relevant source documents
- ranks useful context higher
- generates an answer from retrieved context
- avoids unsupported or hallucinated information
- evaluates the RAG output using DeepEval RAG metrics

## Project Objective

This project focuses on QA evaluation of a RAG system using a professional metric-based testing approach.

The workflow prepares a lightweight QA dataset from EnterpriseRAG-Bench, creates a document store, builds a LangChain-style vector store retrieval flow, generates demo RAG answers, and evaluates the RAG pipeline using DeepEval metrics with Groq as the evaluation judge.

## Dataset Used

This project uses the public EnterpriseRAG-Bench dataset from Hugging Face:

`onyx-dot-app/EnterpriseRAG-Bench`

The dataset contains two main parts:

- `documents` - source documents from company-like systems such as Confluence, GitHub, Gmail, Slack, Jira, and other enterprise tools.
- `questions` - test questions with expected document IDs, gold answers, and answer facts.

For this version of the QA workflow, 10 questions were selected using two filters:

```text
question_type == "basic"
expected_doc_count == 1
```

So the selected test cases are basic questions with one clear expected source document.

## Files

- `README.md` - explains the project objective, dataset, QA workflow, and current status.
- `rag_pipeline_evaluation.ipynb` - clean Google Colab notebook for dataset preparation, document store creation, LangChain vector store retrieval, answer generation, DeepEval tracing, and RAG metric evaluation.
- `lightweight_rag_qa_dataset_with_expected_codes.csv` - lightweight QA dataset created from EnterpriseRAG-Bench.
- `rag_contextual_precision_recall_tracing_result.csv` - final CSV output containing selected test cases and generated actual outputs.
- `rag_document_to_vector_store_flow.jpg` - diagram showing the document-to-vector-store flow.
- `document_store/` - folder created during notebook execution to store exported source documents used by the demo RAG pipeline.

## RAG Evaluation Flow

1. Load EnterpriseRAG-Bench from Hugging Face.
2. Select 10 QA test cases using:

```text
question_type == "basic"
expected_doc_count == 1
```

3. Use `expected_doc_ids` to find matching source documents.
4. Create a lightweight QA dataframe.
5. Export matched source documents into `document_store`.
6. Load `.txt` files from `document_store`.
7. Convert loaded documents into LangChain `Document` objects.
8. Store LangChain documents inside `InMemoryVectorStore`.
9. Retrieve top-k documents using vector similarity search.
10. Generate demo `actual_output` using `google/flan-t5-large` in Colab.
11. Store RAG run data using DeepEval tracing.
12. Evaluate the RAG pipeline using DeepEval metrics with Groq.

## Document-to-Vector-Store Flow

The notebook follows this document processing flow:

```text
.txt files
→ document_store list
→ LangChain Document objects
→ InMemoryVectorStore
→ similarity_search_with_score()
→ top-k retrieved documents
```

Simple meaning:

```text
Raw text documents are converted into searchable vector-store format.
The vector store retrieves the most relevant top-k documents for each question.
```

## Model Usage

This notebook uses two different models for two different purposes.

1. Local Hugging Face model for answer generation

```text
Question + Top-k retrieved documents
→ google/flan-t5-large running in Colab
→ Generated answer / actual_output
```

2. Groq model with DeepEval for evaluation

```text
Question + Gold answer + Retrieved context + Actual output
→ DeepEval metric using Groq
→ Metric score and reason printed in notebook console output
```

Simple meaning:

```text
google/flan-t5-large = generates demo RAG answer
Groq + DeepEval = evaluates RAG quality
```

## DeepEval Metrics Implemented

This project currently implements all 5 main DeepEval RAG metrics:

- Contextual Precision
- Contextual Recall
- Contextual Relevancy
- Answer Relevancy
- Faithfulness

### Contextual Precision

Checks whether the most useful retrieved documents are ranked higher than less useful documents.

Simple:

```text
Are the best documents placed at the top?
```

### Contextual Recall

Checks whether the retrieved documents contain the needed information from the expected answer.

Simple:

```text
Did retrieval bring enough required information?
```

### Contextual Relevancy

Checks whether the retrieved documents are relevant to the question.

Simple:

```text
Are the retrieved documents related to the question?
```

### Answer Relevancy

Checks whether the generated answer actually answers the question.

Simple:

```text
Is the answer relevant to the question?
```

### Faithfulness

Checks whether the generated answer is supported by the retrieved documents.

Simple:

```text
Are the facts in the answer grounded in the retrieved context?
```

## DeepEval Tracing

The notebook uses DeepEval tracing with:

```python
@observe
update_current_trace()
evaluation_dataset.evals_iterator()
```

Tracing records the RAG run data:

```text
question
actual_output
expected_output
retrieval_context
```

DeepEval metrics use this traced data to evaluate the RAG pipeline.

DeepEval prints metric score and reason in the notebook console output.

## Current Status

Completed:

- Lightweight QA dataset prepared using `basic` questions with one expected source document.
- Document store created.
- LangChain vector store retrieval implemented.
- Top-k retrieval implemented using `similarity_search_with_score()`.
- Demo answer generation implemented using `google/flan-t5-large`.
- Groq custom wrapper added for DeepEval evaluation.
- DeepEval tracing implemented.
- All 5 DeepEval RAG metrics implemented.
- Final CSV output generated with 10 test cases and `actual_output`.

Known limitation:

- Groq free-tier rate limits can cause `429 rate_limit_exceeded` during metric evaluation.
- This is an API/free-tier limitation, not a RAG notebook logic issue.

## Current Output

The final CSV contains:

```text
test_id
question
expected_doc_ids
gold_answer
actual_output
```

Metric scores and reasons are viewed from the DeepEval console output in the notebook, because the current implementation follows the traced evaluation flow.
