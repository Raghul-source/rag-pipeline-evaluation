# RAG Pipeline Evaluation Project

This project is a QA testing project for a RAG chatbot.

The goal is to check whether the chatbot:

- retrieves the correct source document
- answers based on the retrieved document
- avoids unsupported or hallucinated information
- gives a pass/fail result for each test question

## Files

- `documents/` - contains sample source documents used by the RAG chatbot
- `golden_dataset.csv` - contains the expected test data, actual RAG output, embedding comparison, and final QA result

## Testing Flow

1. Add source documents inside the `documents/` folder.
2. Create test questions in `golden_dataset.csv`.
3. Run the RAG chatbot or collect model responses.
4. Compare expected document and answer with actual retrieved document and answer.
5. Mark each test case as Pass or Fail with reason.

## Steps of Procedure

1. Prepare test documents.
2. Prepare golden dataset with question, expected document, and expected output.
3. Run the RAG system given by the developer.
4. Capture actual retrieved document and actual output.
5. Pass expected output into the embedding/evaluator script to get expected embedding code.
6. Pass actual output into the same embedding/evaluator script to get actual embedding code.
7. Compare both using similarity score.
8. If similarity score passes the threshold, mark the test case as Pass.
9. If similarity score does not pass the threshold, mark the test case as Fail and write defect remarks.
10. Prepare the final QA report.

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
