
# Gemini RAG Agent

This project implements a Retrieval-Augmented Generation (RAG) agent using Google's Gemini large language model and the LangGraph library. The agent is designed to answer questions based on information extracted from a PDF document, and it includes a classification step to determine if a query is 'on-topic' or 'off-topic' relative to the PDF content.

## Features

- **PDF Context Extraction**: Loads and extracts text content from a specified PDF file.
- **Query Classification**: Determines if a user's question is relevant to the PDF content.
- **Retrieval-Augmented Generation (RAG)**: If 'on-topic', generates an answer strictly based on the PDF context.
- **Off-Topic Handling**: Provides a polite, guardrailed response for irrelevant queries.
- **Modular Workflow**: Utilizes LangGraph for a clear, state-based workflow management.

## Setup

1.  **Install Dependencies**:

    ```bash
    !pip install -U langchain-google-genai langchain-community python-dotenv langgraph fpdf2 pypdf
    ```

2.  **Google Gemini API Key**:

    -   Obtain a `GEMINI_API_KEY` from Google AI Studio.
    -   In Google Colab, add your API key to the secrets manager (accessible via the 🔑 icon on the left sidebar) and name it `GEMINI_API_KEY`.

3.  **PDF Document**:

    -   Ensure you have a PDF file available. By default, the code creates a sample PDF if one isn't found at `/content/S Santhosh Kumar Resume.pdf`. You can replace this with your desired PDF file path.

## Usage

1.  **Run the Notebook Cells**:

    Execute all the code cells in the notebook sequentially.

2.  **Interact with the Agent**:

    Once the agent initializes, you will be prompted to `Enter your Query:`.

    -   Type your question related to the PDF content (e.g., "What is the remote work policy?").
    -   Type a question unrelated to the PDF (e.g., "What is the capital of France?") to see the off-topic handling.
    -   Type `stop` to exit the chat.

## Code Structure

### AgentState (TypedDict)

Defines the shared state that flows through the LangGraph workflow:

-   `pdf_path`: Path to the input PDF file.
-   `question`: The user's query.
-   `category`: Classification of the query ('on_topic' or 'off_topic').
-   `context`: Extracted text content from the PDF.
-   `answer`: The final generated response.

### Graph Nodes (Actions)

-   `load_pdf_context`: Reads the PDF and extracts all text.
-   `classify_input`: Uses Gemini to categorize the user's question against the PDF context.
-   `retrieve_and_answer`: If 'on-topic', uses Gemini to answer the question based *only* on the PDF context.
-   `handle_off_topic`: Provides a predefined response for 'off-topic' queries.

### Router (`route_question`)

A conditional edge function that directs the workflow based on the `category` in the `AgentState`. It routes to `retrieve_and_answer` for 'on_topic' questions and `handle_off_topic` otherwise.

### Workflow Construction (`StateGraph`)

Defines the flow of execution:

-   Starts with `load_pdf_context`.
-   Proceeds to `classify_input`.
-   Uses `route_question` to conditionally move to either `retrieve_and_answer` or `handle_off_topic`.
-   Both answer nodes lead to the `END` of the graph.

