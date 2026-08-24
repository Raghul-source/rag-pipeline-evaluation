# DeepEval Learning Notes

This file is used for learning DeepEval concepts and keeping reusable notes for future AI testing projects.

It is not a project execution plan. It is a reference file for understanding which DeepEval evaluation style and metric should be used for different AI application types.

---

## 1. DeepEval Basics

DeepEval is an evaluation framework used to test LLM applications such as:

- RAG pipelines
- chatbots
- AI agents
- summarizers
- classifiers
- tool-calling systems

DeepEval helps QA engineers check whether an AI application is producing useful, relevant, grounded, and safe outputs.

Important basic concepts:

- **Golden**: The input and expected result used for evaluation.
- **LLMTestCase**: A single-turn test case used to evaluate one input-output interaction.
- **ConversationalTestCase**: A multi-turn test case used to evaluate full conversations.
- **Metric**: A scoring method used to judge AI behavior.
- **Trace**: A recorded execution of an AI application.
- **Span**: One internal step inside a trace, such as retrieval, generation, tool call, or sub-agent execution.

---

## 2. DeepEval Metrics Guide: When to Use Which Metric

This guide provides a structured overview of DeepEval metrics, categorized by AI application type.

Use this reference to determine which metric to apply based on the evaluation need.

---

## 2.1 RAG (Retrieval-Augmented Generation) Metrics

Use these metrics when the AI pipeline retrieves external documents before generating answers.

| Metric | Purpose | Required Test Case Parameters |
| --- | --- | --- |
| **`FaithfulnessMetric`** | Evaluates if the AI answer is factually grounded in the retrieved context. It helps detect hallucinations or unsupported claims. | `input`, `actual_output`, `retrieval_context` |
| **`AnswerRelevancyMetric`** | Evaluates if the AI final answer stays on-topic and directly answers the user question. | `input`, `actual_output` |
| **`ContextualRelevancyMetric`** | Evaluates whether the retrieved context is relevant to the user query. | `input`, `retrieval_context` |
| **`ContextualPrecisionMetric`** | Measures whether the most relevant information is ranked at the top of the retrieved context. | `input`, `retrieval_context`, `expected_output` |
| **`ContextualRecallMetric`** | Evaluates whether the retriever fetched the necessary information required to answer the query. | `input`, `retrieval_context`, `expected_output` |

Simple RAG metric meaning:

```text
Contextual Precision = Are useful documents ranked higher?
Contextual Recall = Did retrieval bring enough required information?
Contextual Relevancy = Are retrieved documents relevant to the question?
Answer Relevancy = Does the answer address the question?
Faithfulness = Is the answer supported by retrieved context?
```

---

## 2.2 AI Agent Metrics

Use these metrics when the AI application performs multi-step tasks, creates execution plans, or invokes external tools.

| Metric | Purpose | Scope |
| --- | --- | --- |
| **`TaskCompletionMetric`** | Assesses whether the agent successfully completed the assigned overall task. | Trajectory-based |
| **`StepEfficiencyMetric`** | Assesses whether the agent completed the task efficiently without redundant or unnecessary steps. | Trajectory-based |
| **`PlanAdherenceMetric`** | Measures whether the agent followed its initially generated execution plan. | Trajectory-based |
| **`PlanQualityMetric`** | Evaluates whether the agent generated a logical, complete, and efficient plan. | Trajectory-based |
| **`ToolCorrectnessMetric`** | Assesses whether the agent selected the correct tools during execution. | Component-level |
| **`ArgumentCorrectnessMetric`** | Assesses whether the agent supplied the correct arguments into those tools. | Component-level |

---

## 2.3 Chatbot (Multi-Turn) Metrics

Use these metrics when evaluating conversational agents that maintain context across multiple turns.

| Metric | Purpose |
| --- | --- |
| **`RoleAdherenceMetric`** | Evaluates if the chatbot stays in character and maintains its persona throughout the conversation. |
| **`KnowledgeRetentionMetric`** | Measures whether the chatbot remembers and uses information learned earlier in the dialogue. |
| **`ConversationCompletenessMetric`** | Assesses whether the full conversation resolved the user's need. |
| **`ConversationRelevancyMetric`** | Measures if responses remain relevant to the ongoing user inputs across turns. |

---

## 2.4 Safety & Security Metrics

Use these metrics to monitor and prevent harmful, toxic, or insecure behavior from the LLM.

| Metric | Purpose |
| --- | --- |
| **`BiasMetric`** | Detects unfair bias, prejudice, or stereotyping in LLM outputs. |
| **`ToxicityMetric`** | Detects rude, offensive, hateful, or toxic language. |
| **`PIILeakageMetric`** | Checks if the AI accidentally leaks private or sensitive user information. |
| **`NonAdviceMetric`** | Evaluates whether the model avoids giving unauthorized professional advice, such as medical or legal advice. |

---

## 2.5 Custom & Subjective Metrics

Use these frameworks when standard metrics do not cover unique business logic or subjective evaluation criteria.

| Metric / Framework | Purpose |
| --- | --- |
| **`GEval`** | Lets you define custom evaluation criteria in plain English, such as rating professionalism, clarity, helpfulness, or tone. |
| **`DAG` (Deep Acyclic Graph)** | Decision-tree-based evaluation method for objective or mixed multi-step evaluation rules. |

---

## 3. DeepEval End-to-End Evaluation Guide

End-to-end evaluation checks the application output at the full app level.

There are two main types:

- single-turn end-to-end evaluation
- multi-turn end-to-end evaluation

---

## 3.1 Single-Turn End-to-End Evaluation

### Overview

- **Definition:** Evaluates LLM applications where one input maps to one output per interaction.
- **Use Cases:** Standard Q&A, RAG pipelines, text summarization, content classification, and writing assistants.
- **Test Case Model:** `LLMTestCase`

### Execution Approaches

#### Approach A: `evals_iterator()` with Tracing

This is useful when you can instrument your application code.

It automatically captures application execution traces and links them to test cases.

Common implementation methods:

- `@observe`
- `update_current_trace()`
- native framework integrations such as LangChain, LlamaIndex, or OpenAI integrations

Benefits:

- captures richer execution data
- supports traced evaluation
- gives a path toward component-level evaluation

#### Approach B: `evaluate()` Without Tracing

This is useful when you cannot modify the application source code.

In this approach:

1. Manually run the application.
2. Collect input and output.
3. Create `LLMTestCase` objects.
4. Pass test cases into `evaluate()`.

Simple difference:

```text
evals_iterator() + tracing = app is observed while it runs
evaluate() = test cases are manually prepared and evaluated
```

---

## 3.2 Multi-Turn End-to-End Evaluation

### Overview

- **Definition:** Evaluates entire conversations instead of isolated single-turn responses.
- **Use Cases:** Chatbots, conversational assistants, and customer support agents.
- **Test Case Model:** `ConversationalTestCase`

### Implementation Workflow

1. **Wrap the chatbot**

   Write a `model_callback` function that tells DeepEval how to call the chatbot.

2. **Build a dataset**

   Create `ConversationalGolden` items containing:

   - scenario
   - expected outcome
   - user persona

3. **Simulate turns**

   Use `ConversationSimulator` to simulate a user conversation with the chatbot.

4. **Run `evaluate()`**

   Pass the generated `ConversationalTestCase` objects and multi-turn metrics into `evaluate()`.

---

## 4. Component-Level LLM Evaluation (Step-Wise Evals)

Component-level evaluation grades the internal components of an LLM application rather than treating the entire system as one black box.

Examples of internal components:

- retriever
- tool call
- inner LLM generation
- sub-agent
- planner
- router

---

## 4.1 Key Concepts

- **Span:** One internal component execution inside a trace.
- **Component-level test case:** An `LLMTestCase` attached to a specific span.
- **Scope mixing:** Trace-level metrics can evaluate the full flow, while component-level metrics can evaluate internal spans.

Simple meaning:

```text
End-to-end evaluation = test the full app result
Component-level evaluation = test one internal step separately
```

---

## 4.2 How Component-Level Evaluation Works

1. The instrumented LLM app generates a trace.
2. The trace contains multiple spans.
3. Metrics are attached to the specific spans/components that need evaluation.
4. `evals_iterator()` runs the dataset one golden at a time.
5. Each metric-attached span is evaluated independently.
6. Trace data, span test cases, and metric results are grouped into one test run.

---

## 4.3 Component-Level Example

Example using manual `@observe` instrumentation:

```python
import asyncio
from deepeval.tracing import observe, update_current_span, update_current_trace
from deepeval.test_case import LLMTestCase
from deepeval.metrics import AnswerRelevancyMetric


@observe()
async def my_ai_agent(query: str) -> str:
    chunks = await retrieve(query)
    answer = await generate(query, chunks)
    update_current_trace(input=query, output=answer)
    return answer


@observe()
async def retrieve(query: str) -> list[str]:
    return ["Relevant document chunks..."]


@observe(metrics=[AnswerRelevancyMetric()])
async def generate(query: str, chunks: list[str]) -> str:
    response = "Generated answer from LLM..."

    update_current_span(
        test_case=LLMTestCase(
            input=query,
            actual_output=response,
            retrieval_context=chunks
        )
    )

    return response


async def run_evals():
    for golden in dataset.evals_iterator():
        task = asyncio.create_task(my_ai_agent(golden.input))
        dataset.evaluate(task)


asyncio.run(run_evals())
```

---

## 4.4 When to Use Component-Level Evaluation

Use component-level evaluation when you want to prove a specific internal part is working correctly.

For a RAG project, useful component-level checks can be:

- retriever quality
- answer generation quality
- tool call correctness
- context construction correctness

For the current RAG evaluation project:

```text
Current implementation = traced RAG evaluation
Future improvement = separate component-level evaluation for retriever and answer generation
```

---

## 5. Mapping DeepEval Concepts to This RAG Project

| DeepEval Concept | Current RAG Project Usage |
| --- | --- |
| Golden | Each selected QA row from `qa_df` |
| `evals_iterator()` | Runs each QA row through traced evaluation |
| `@observe` | Used on `run_traced_rag_pipeline()` |
| `update_current_trace()` | Stores `actual_output`, `expected_output`, and `retrieval_context` |
| `ContextualPrecisionMetric` | Checks if useful retrieved contexts are ranked higher |
| `ContextualRecallMetric` | Checks if retrieved contexts contain enough required information |
| `ContextualRelevancyMetric` | Checks if retrieved contexts are relevant to the question |
| `AnswerRelevancyMetric` | Checks if generated answer answers the question |
| `FaithfulnessMetric` | Checks if generated answer is grounded in retrieved context |
| Groq | Evaluation judge model used by DeepEval |
| `google/flan-t5-large` | Local Hugging Face model used for demo answer generation |

---

## 6. Notes for Future Learning

Topics to learn later:

- component-level evaluation for retriever span
- component-level evaluation for answer generation span
- trajectory-based evaluation for AI agents
- tool correctness and argument correctness
- multi-turn chatbot evaluation
- safety metrics
- custom metrics using `GEval`
- CI/CD evaluation using `deepeval test run`
