# Coding Agent

A modular, code-executing AI agent designed to solve complex tasks by autonomously generating and running Python snippets. Built with a focus on extensibility, security, and multi-model support.

## Overview

Coding Agent is a "ReAct" style agent that approaches problem-solving through a cycle of **Thought**, **Code**, and **Observation**. Instead of just generating text, it writes Python code to interact with tools, perform computations, and process data, then uses the execution results to refine its next steps.

### Key Features

- **Autonomous Problem Solving**: Implements a robust reasoning loop to break down and solve multi-step tasks.
- **Secure Code Execution**: Utilizes `smolagents`'s `LocalPythonExecutor` to run generated code in a controlled environment with restricted imports.
- **Web Intelligence**: Integrated tools for web searching (DuckDuckGo, Tavily) and webpage content extraction (HTML-to-Markdown).
- **LLM Agnostic**: Powered by `litellm`, allowing you to use any major LLM provider (OpenAI, Anthropic, Google, Mistral, etc.) by simply changing an environment variable.
- **Jinja2 Templating**: Dynamic system prompts that automatically adapt based on available tools and configurations.

---

## Architecture

The project is designed with a clean separation of concerns:

### 1. The Agent Loop (`agent.py`)
The core `Agent` class manages the conversation history and the execution lifecycle:
- **Initialization**: Configures the model, tools, and authorized Python imports.
- **System Prompt**: Uses Jinja2 to inject tool descriptions and rules into the base prompt.
- **Execution Step**: 
    1.  **Thought**: The LLM generates reasoning and a Python code block.
    2.  **Action**: The agent extracts the code using regex.
    3.  **Execution**: `LocalPythonExecutor` runs the code and captures logs/output.
    4.  **Observation**: The output is fed back to the LLM as a new user message.

### 2. Tooling System (`tools.py`)
Tools are defined as classes with a standard interface, making it easy to add new capabilities:
- `DuckDuckGoSearchTool`: Provides free, no-auth web search results.
- `TavilySearchTool`: Optimized for LLM agents, providing high-quality search snippets.
- `VisitWebpageTool`: Fetches a URL, converts it to clean Markdown, and handles truncation for context efficiency.
- `FinalAnswerTool`: A special tool used by the agent to return the ultimate result to the user.

### 3. Prompt Management (`prompts/`)
System prompts are stored as templates, ensuring the agent understands its boundaries, the "Thought-Code-Observation" format, and how to use the provided tools.

---

## 🛠️ Getting Started

### Prerequisites

- Python 3.12 or higher
- [Poetry](https://python-poetry.org/docs/#installation) for dependency management

### Installation

1.  **Clone the repository**:
    ```bash
    git clone <repository-url>
    cd coding-agent
    ```

2.  **Install dependencies**:
    ```bash
    poetry install
    ```

3.  **Configure Environment**:
    Create a `.env` file in the root directory (refer to `.env.example`):
    ```env
    MODEL="openai/gpt-4o"  # Or your preferred model
    OPENAI_API_KEY="your_key_here"
    TAVILY_API_KEY="your_tavily_key_if_used"
    ```

### Running the Agent

Start the interactive CLI:
```bash
poetry run python main.py
```

---

## Usage Example

**Task**: *"Find the current price of Bitcoin and calculate how much 0.5 BTC is worth in USD."*

1.  **Thought**: I need to find the current price of Bitcoin using a search tool.
2.  **Code**: `print(web_search("current bitcoin price in USD"))`
3.  **Observation**: "Bitcoin is currently trading at $65,000."
4.  **Thought**: Now I can calculate the value of 0.5 BTC.
5.  **Code**: `final_answer(0.5 * 65000)`
6.  **Final Answer**: 32500

---

## Security Note

The agent uses a `LocalPythonExecutor` which restricts execution to a set of "authorized imports" (e.g., `math`, `datetime`, `re`). While this provides a layer of protection, always exercise caution when allowing an LLM to execute code on your local machine.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
