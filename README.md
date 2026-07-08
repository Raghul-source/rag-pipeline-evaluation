# RAG Pipeline Evaluation Project

This project is a QA testing project for a RAG chatbot.

The goal is to check whether the chatbot:

- retrieves the correct source document
- answers based on the retrieved document
- avoids unsupported or hallucinated information
- gives a pass/fail result for each test question

## Project Objective

This project focuses on QA evaluation of a RAG system. The goal is not to build the RAG chatbot, but to prepare a QA workflow that can verify whether a RAG chatbot retrieves the correct document and generates an answer that matches the expected answer.

The project currently prepares a lightweight QA dataset, generates expected embedding fingerprint codes, and demonstrates one sample actual-output comparison using semantic similarity.

## Dataset Used

This project uses the public EnterpriseRAG-Bench dataset from Hugging Face: `onyx-dot-app/EnterpriseRAG-Bench`.

The dataset contains two main parts:

- `documents` - source documents from company-like systems such as Confluence, GitHub, Gmail, Slack, Jira, and other enterprise tools.
- `questions` - test questions with expected document IDs, gold answers, and answer facts.

For the first version of this QA workflow, 10 `basic` questions were selected. Each selected question has one clear expected source document, which makes the evaluation easier to verify.

## Files

- `README.md` - explains the project objective, dataset, QA workflow, and current status.
- `RAG_Pipeline_Evaluation_ETL_and_Embedding_Check.ipynb` - Google Colab notebook used for ETL, expected embedding code generation, and demo comparison.
- `lightweight_rag_qa_dataset_with_expected_codes.csv` - lightweight QA dataset created from EnterpriseRAG-Bench with expected answers and expected embedding codes.
- `rag_qa_demo_comparison_result.csv` - demo result file showing one actual-output comparison with similarity score and pass/fail status.
- `documents/` - sample local documents kept for reference.

## Testing Flow

1. Load EnterpriseRAG-Bench from Hugging Face.
2. Select 10 basic questions from the `questions` dataset.
3. Use `expected_doc_ids` to find the matching source documents from the `documents` dataset.
4. Create a lightweight QA dataset with question, expected document, gold answer, and answer facts.
5. Generate expected embedding fingerprint codes from the gold answers.
6. When a RAG chatbot/model is available, run each question against the system and store the response in `actual_output`.
7. Generate actual embedding fingerprint codes from the model responses.
8. Compare expected answer and actual output using semantic similarity score.
9. Mark each test case as `Pass`, `Fail`, or `Not Run` with remarks.

## Steps of Procedure

1. Prepare a lightweight QA dataset from EnterpriseRAG-Bench.
2. Generate expected embedding fingerprint codes from the gold answers.
3. Add actual RAG model outputs when the system is available.
4. Compare expected and actual outputs using semantic similarity.
5. Mark each test case as Pass, Fail, or Not Run with remarks.

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

Question dataset gives the correct document ID.

The matching document is taken from the documents dataset.

The RAG system receives/searches the selected source documents.

The actual retrieved document is compared with the expected document ID.

The actual answer is compared with the gold answer and answer facts.

## Current Status and Next Phase

Current status:

- Lightweight QA dataset is prepared.
- Expected embedding fingerprint codes are generated.
- One demo actual output comparison is completed.
- Similarity score and Pass/Fail logic are verified.

Next phase:

- Run the selected questions against the actual RAG chatbot/model.
- Fill the `actual_output` column with model responses.
- Run the comparison logic for all test cases.
- Prepare the final QA report with Pass, Fail, similarity score, and remarks.
