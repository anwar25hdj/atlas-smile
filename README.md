
Atlas Smile

«Production-oriented RAG system built with n8n, OpenAI, Supabase, Telegram, PostgreSQL, and Google Sheets.»

Atlas Smile is an end-to-end Retrieval-Augmented Generation (RAG) system designed to ingest PDF-based knowledge, transform it into searchable vector representations, and use that knowledge to answer user questions through Telegram.

The project is composed of multiple specialized n8n workflows covering knowledge ingestion, RAG-based question answering, evaluation, memory, logging, and error handling.

---

Table of Contents

- "Overview" (#overview)
- "Architecture" (#architecture)
- "Core Workflows" (#core-workflows)
- "1. Knowledge Ingestion" (#1-knowledge-ingestion)
- "2. RAG Query" (#2-rag-query)
- "3. Evaluation" (#3-evaluation)
- "RAG Pipeline" (#rag-pipeline)
- "Memory" (#memory)
- "Logging" (#logging)
- "Error Handling" (#error-handling)
- "Evaluation Results" (#evaluation-results)
- "Tech Stack" (#tech-stack)
- "Project Structure" (#project-structure)
- "Data Flow" (#data-flow)
- "Security" (#security)
- "Limitations" (#limitations)
- "Future Improvements" (#future-improvements)
- "Conclusion" (#conclusion)

---

Overview

Atlas Smile solves a common knowledge-retrieval problem:

How can users ask natural-language questions about a collection of documents without manually searching through them?

The system converts source documents into structured, embedded knowledge and stores them in a vector database. When a user asks a question, the system retrieves relevant information and provides an AI-generated response based on the available knowledge.

The system also includes a dedicated evaluation workflow to test response quality against a predefined set of questions.

Key capabilities

- PDF document ingestion
- Document extraction and preprocessing
- Text transformation
- Vector embeddings
- Semantic retrieval
- AI-powered question answering
- Conversational memory
- Telegram interface
- Conversation logging
- Automated evaluation
- Centralized error handling

---

Architecture

                         ┌─────────────────────┐
                         │ Source Documents │
                         │ PDF │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Ingestion Workflow │
                         │ Extract → Transform │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Embeddings │
                         │ OpenAI │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Supabase Vector │
                         │ Store │
                         └──────────┬──────────┘
                                    │
                                    │ Retrieval
                                    ▼
┌──────────────┐ ┌─────────────────────┐
│ Telegram │───────▶│ RAG Query │
│ User │ │ AI Agent │
└──────────────┘ └──────────┬──────────┘
                                   │
                     ┌─────────────┼─────────────┐
                     ▼ ▼ ▼
                  RAG PostgreSQL Tools
               Retrieval Memory
                     │ │
                     └──────┬──────┘
                            ▼
                    ┌───────────────┐
                    │ Response │
                    │ Telegram │
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │ Google Sheets │
                    │ Logging │
                    └───────────────┘


                    ┌─────────────────┐
                    │ Evaluation │
                    │ 28 Test Cases │
                    └────────┬────────┘
                             │
                             ▼
                       RAG Query

---

Core Workflows

Atlas Smile is divided into three primary workflows.

Workflow| Responsibility
"atlas-smile-ingestion"| Ingest and prepare source documents
"atlas-smile-rag-query"| Process user questions and generate RAG responses
"atlas-smile-evaluation"| Evaluate the RAG system using predefined test cases

A dedicated error-handling mechanism is also used to capture and log workflow failures.

---

1. Knowledge Ingestion

Workflow

"atlas-smile-ingestion"

The ingestion workflow prepares source documents for semantic retrieval.

Pipeline

Trigger
   ↓
Search Files & Folders
   ↓
Download File
   ↓
Extract From File
   ↓
Edit Fields
   ↓
JavaScript Processing
   ↓
JavaScript Processing
   ↓
JavaScript Processing
   ↓
Supabase Vector Store

Vector Processing

The vector store is connected to the required document-processing components:

Document
   │
   ├── Embeddings
   │ └── OpenAI Embeddings
   │
   └── Text Splitting
          └── Recursive Character Text Splitter

This converts the source content into a form suitable for semantic retrieval.

---

2. RAG Query

Workflow

"atlas-smile-rag-query"

This is the primary user-facing workflow.

Users interact with the system through Telegram. The incoming question is processed by an AI Agent connected to the project's knowledge base and conversational memory.

Main flow

Telegram Input
       ↓
AI / RAG Core
       ↓
Knowledge Retrieval
       ↓
AI Agent
       ↓
Telegram Response
       ↓
Conversation Logging

AI / RAG Core

The AI Agent is connected to:

- OpenAI Chat Model
- Supabase Vector Store
- OpenAI Embeddings
- PostgreSQL Chat Memory

This allows the agent to combine retrieved knowledge with conversational context.

---

3. Evaluation

Workflow

"atlas-smile-evaluation"

The evaluation workflow is used to test the system independently from normal user interaction.

It contains 28 different test questions designed to cover multiple question types and scenarios.

The evaluation workflow can execute the RAG query system and record the resulting outputs for analysis.

Evaluation objective

The goal is not simply to determine whether an answer sounds plausible.

The evaluation focuses on whether the system:

1. Retrieves the relevant information.
2. Produces factually correct answers.
3. Provides sufficiently complete responses.
4. Expresses the answer clearly.

---

RAG Pipeline

The system follows the standard RAG architecture:

Documents
    ↓
Extraction
    ↓
Preprocessing
    ↓
Text Splitting
    ↓
Embeddings
    ↓
Vector Store
    ↓
User Question
    ↓
Semantic Retrieval
    ↓
Relevant Context
    ↓
AI Agent
    ↓
Final Answer

The important design principle is that the language model is supported by retrieved project knowledge rather than relying exclusively on its pretrained knowledge.

---

Memory

Atlas Smile uses PostgreSQL Chat Memory to maintain conversational context.

This allows the system to handle conversations where subsequent questions depend on previous messages.

Conceptually:

User Message
      ↓
AI Agent
 ↙ ↘
Memory RAG
 ↓ ↓
Context Knowledge
 ↘ ↙
   Final Response

Memory and retrieval serve different purposes:

- Memory provides conversational context.
- RAG provides external project knowledge.

---

Logging

Conversation and workflow information is logged using Google Sheets.

The logging layer provides a simple and accessible way to inspect system activity and review conversations.

The architecture separates the logging process from the core RAG reasoning process, allowing the system to retain operational records without making the logging layer responsible for generating answers.

---

Error Handling

Atlas Smile includes dedicated error-handling logic for workflow failures.

The objective is to prevent failures from becoming silent and to make execution problems easier to identify and investigate.

The error-handling layer records relevant execution information and sends it to the logging layer.

Conceptually:

Workflow Execution
       │
       ▼
     Error
       │
       ▼
Error Handling
       │
       ▼
Format / Process Error
       │
       ▼
Google Sheets

The system also distinguishes between normal application behavior and actual execution failures.

For example, a question for which the knowledge base does not provide sufficient information should be treated as a controlled application response rather than automatically being treated as a workflow failure.

---

Evaluation Results

The current evaluation dataset contains:

28 test cases

In the latest evaluation:

- Factual correctness: 100% according to the test results
- Overall answer quality: approximately 96%

The remaining quality difference was attributed to answer completeness and wording quality in some responses, rather than factual errors in the tested answers.

This distinction is important because factual correctness and response quality measure different aspects of an RAG system.

«Evaluation results represent the current internal test set and should not be interpreted as a universal accuracy measurement for all possible inputs.»

---

Tech Stack

Technology| Role
n8n| Workflow orchestration and automation
OpenAI| Chat model and embeddings
Supabase| Vector storage and retrieval
PostgreSQL| Conversational memory
Telegram| User-facing interface
Google Sheets| Conversation and execution logging
JavaScript| Data transformation and preprocessing

---

Project Structure

Atlas Smile
│
├── atlas-smile-ingestion
│ ├── File discovery
│ ├── File download
│ ├── PDF extraction
│ ├── Data preprocessing
│ ├── Text processing
│ ├── Embeddings
│ └── Supabase Vector Store
│
├── atlas-smile-rag-query
│ ├── Telegram input
│ ├── Evaluation input
│ ├── AI Agent
│ ├── OpenAI Chat Model
│ ├── PostgreSQL Memory
│ ├── Supabase Vector Store
│ ├── Telegram response
│ ├── Google Sheets logging
│ └── Error handling
│
└── atlas-smile-evaluation
    ├── Evaluation input
    ├── Test cases
    ├── RAG execution
    └── Result analysis

---

Data Flow

Document ingestion

PDF
 ↓
Extract
 ↓
Clean / Transform
 ↓
Split
 ↓
Embed
 ↓
Supabase Vector Store

User query

Telegram
 ↓
Question
 ↓
AI Agent
 ↓
Retrieve relevant knowledge
 ↓
Combine with conversational memory
 ↓
Generate response
 ↓
Telegram
 ↓
Log conversation

Evaluation

Test Question
 ↓
RAG Query
 ↓
Generated Answer
 ↓
Evaluation
 ↓
Result

---

Security

Sensitive credentials should never be committed to the repository.

This includes:

- API keys
- Telegram bot tokens
- Database credentials
- OAuth credentials
- Supabase keys
- Environment variables containing secrets

Credentials should be managed through n8n credentials or environment-level secret management.

If screenshots are published, sensitive identifiers and credentials should also be removed or blurred.

---

Limitations

The current system has several practical limitations.

Evaluation scope

The evaluation is based on a predefined set of 28 test cases. It provides useful evidence about the current system but does not represent every possible user question.

Knowledge boundaries

The system's answers depend on the information available in the indexed knowledge base.

Response quality

Although the tested answers are factually correct, some responses can still be improved in terms of completeness and phrasing.

Infrastructure dependency

The system depends on external services such as OpenAI, Supabase, Telegram, and Google Sheets.

Service availability, API limits, or configuration issues can therefore affect execution.

---

Future Improvements

Potential future improvements include:

- Expanding the evaluation dataset.
- Adding more granular evaluation metrics.
- Improving answer completeness and response formatting.
- Adding richer monitoring and observability.
- Introducing more sophisticated retrieval strategies.
- Adding metadata-based retrieval and filtering where appropriate.
- Improving failure recovery for transient API errors.
- Adding automated regression testing for future workflow changes.

---

Conclusion

Atlas Smile demonstrates an end-to-end implementation of an AI-powered RAG system using workflow automation.

Rather than being a single AI Agent workflow, the project separates the system into dedicated components for:

Ingestion → Retrieval → Generation → Memory → Response → Logging → Evaluation → Error Handling

The architecture is designed to make the system easier to test, maintain, and extend while providing a measurable evaluation process for its responses.

---

Project Status

Status: Completed

Current evaluation set: 28 test cases

Latest factual correctness: 100%

Latest overall answer quality: ~96%

Primary orchestration platform: n8n
