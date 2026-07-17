# RAG Pipeline Evaluation Project

This project is a QA testing project for a RAG chatbot.

The goal is to check whether the chatbot:

- retrieves the correct source document
- answers based on the retrieved document
- avoids unsupported or hallucinated information
- gives a pass/fail result for each test question

## Project Objective

This project focuses on QA evaluation of a RAG system. The goal is to prepare a QA workflow that can verify whether a RAG chatbot retrieves the correct document and generates an answer that matches the expected answer.

The project prepares a lightweight QA dataset, generates expected embedding fingerprint codes, creates a document store, runs a demo RAG pipeline to generate actual outputs, and evaluates the results using retrieval match, semantic similarity, fact-level meaning check, and overall QA status.

## Dataset Used

This project uses the public EnterpriseRAG-Bench dataset from Hugging Face: `onyx-dot-app/EnterpriseRAG-Bench`.

The dataset contains two main parts:

- `documents` - source documents from company-like systems such as Confluence, GitHub, Gmail, Slack, Jira, and other enterprise tools.
- `questions` - test questions with expected document IDs, gold answers, and answer facts.

For the first version of this QA workflow, 10 `basic` questions were selected. Each selected question has one clear expected source document, which makes the evaluation easier to verify.

## Files

- `README.md` - explains the project objective, dataset, QA workflow, and current status.
- `RAG_Pipeline_Evaluation_ETL_and_Embedding_Check.ipynb` - Google Colab notebook used for ETL, expected embedding code generation, document store creation, demo RAG pipeline, hybrid retrieval, chunk-based answer extraction, semantic similarity comparison, fact-level checking, and overall QA status.
- `lightweight_rag_qa_dataset_with_expected_codes.csv` - lightweight QA dataset created from EnterpriseRAG-Bench with expected answers and expected embedding codes.
- `rag_qa_final_demo_result_with_all_checks.csv` - final demo result file showing retrieval match status, similarity score, fact-level check, overall QA status, and remarks.
- `document_store/` - created during notebook execution to store exported source documents used by the demo RAG pipeline.

## Testing Flow

1. Load EnterpriseRAG-Bench from Hugging Face.
2. Select basic questions from the `questions` dataset.
3. Use `expected_doc_ids` to find the matching source documents from the `documents` dataset.
4. Create a lightweight QA dataset with question, expected document ID, gold answer, and answer facts.
5. Generate expected embedding fingerprint codes from the gold answers.
6. Export source documents into a `document_store` folder during notebook execution.
7. Run the demo RAG pipeline using hybrid retrieval.
8. Generate `actual_retrieved_doc_ids` and `actual_output`.
9. Generate actual embedding fingerprint codes from the demo RAG outputs.
10. Check whether the actual retrieved document matches the expected document ID.
11. Compare expected answer and actual output using semantic similarity score.
12. Check whether expected answer facts are present in the actual output using fact-level meaning check.
13. Combine retrieval, similarity, and fact-check results into an overall QA status.
14. Mark each test case as `Pass`, `Fail`, or `Not Run` with detailed remarks.

## Steps of Procedure

1. Prepare a lightweight QA dataset from EnterpriseRAG-Bench.
2. Generate expected embedding fingerprint codes from the gold answers.
3. Create a `document_store` folder from the expected source documents.
4. Run demo RAG retrieval using hybrid search.
5. Generate actual retrieved document IDs and actual outputs using chunk-based answer extraction.
6. Check retrieval match, semantic similarity, and fact-level meaning match.
7. Generate overall QA status as Pass, Fail, or Not Run with detailed remarks.

## Question Selection Plan

For the first version, only a small set of questions will be selected to keep the QA workflow simple and easy to verify.

- Start with 10 questions.
- Prefer `basic` question type first.
- Select questions that have one clear expected document.
- Avoid complex multi-document questions in the first version.
- Avoid `info not found` questions in the first version.

## How Source Documents Are Selected

Each question in EnterpriseRAG-Bench has `expected_doc_ids`. These IDs tell which document should contain the correct answer.

For each selected question:

1. Read the `expected_doc_ids`.
2. Find the matching document from the `documents` dataset using `doc_id`.
3. Use only those matched documents as the source documents for the lightweight QA dataset.

## QA Flow

The question dataset provides the expected document ID and expected answer.

The matching source document is exported into `document_store` during notebook execution.

The demo RAG pipeline reads documents from `document_store`.

Hybrid retrieval selects the most relevant document using keyword matching and embedding similarity.

The actual retrieved document ID is compared with the expected document ID.

The actual answer is generated from the retrieved document using chunk-based answer extraction.

The actual answer is compared with the gold answer using semantic similarity.

The expected answer facts are checked against the actual output using fact-level meaning check.

The retrieval result, similarity result, and fact-check result are combined into an overall QA status with detailed remarks.

## Current Status and Next Phase

Current status:

- Lightweight QA dataset is prepared.
- Expected embedding fingerprint codes are generated.
- `document_store` creation is added during notebook execution.
- Demo RAG pipeline is added using hybrid retrieval and chunk-based answer extraction.
- Retrieval match, semantic similarity, fact-level meaning check, and overall QA status logic are verified.
- Detailed failure remarks are added for failed cases.

Latest result summary:

- Retrieval match: 9 Pass, 1 Fail.
- Semantic similarity: 1 Pass, 9 Fail.
- Fact-level check: 7 Pass, 3 Fail.
- Overall QA status: 1 Pass, 9 Fail.

Next phase:

- Improve demo RAG answer quality so `actual_output` becomes more direct and less noisy.
- Reduce extra/unwanted text in generated answers.
- Re-run retrieval, similarity, fact-check, and overall QA logic after improvements.
- Prepare the final QA report with retrieval status, similarity score, fact match score, overall status, missing facts, and remarks.
