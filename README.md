LLM Analysis - Autonomous Quiz Solver Agent
===========================================

An intelligent, autonomous agent built with **LangGraph** and **LangChain** that solves data-related quizzes involving web scraping, data processing, analysis, and visualization tasks. The system uses **Google's Gemini 2.5 Flash** model to orchestrate tool usage and make decisions.

🔍 Overview
-----------

This project functions as an autonomous agent capable of solving multi-step quiz tasks. It receives a starting URL via API, navigates through questions, and utilizes a suite of tools to:

-   **Source Data**: Scrape websites and render JavaScript-heavy pages.

-   **Process Files**: Download and handle CSVs, PDFs, and images.

-   **Analyze**: Write and execute Python code for data cleaning and statistical analysis.

-   **Submit**: autonomously submit answers to the quiz server.

🏗️ Architecture
----------------

The project uses a **LangGraph state machine** architecture:

1.  **FastAPI Server**: Handles incoming HTTP requests (`/solve`) and triggers the agent.

2.  **Orchestrator (LangGraph)**: Manages the conversation state and decision-making loop.

3.  **Brain (Gemini 2.5 Flash)**: Analyzes the current task and decides which tool to call.

4.  **Tools Layer**: A set of specific Python functions for scraping, coding, and networking.

📁 Project Structure
--------------------

```
Project2-LLM-Analysis-Quiz/
├── agent.py                    # LangGraph state machine & agent logic
├── main.py                     # FastAPI application entry point
├── Dockerfile                  # Container configuration
├── pyproject.toml              # Project dependencies (uv/pip)
├── .env                        # Environment variables (created by user)
├── tools/
│   ├── __init__.py
│   ├── web_scraper.py          # Playwright-based HTML renderer
│   ├── code_generate_and_run.py # Python code execution sandbox
│   ├── download_file.py        # File downloader
│   ├── send_request.py         # HTTP POST tool for answers
│   └── add_dependencies.py     # Dynamic package installer
└── README.md

```

🛠️ Prerequisites & Installation
--------------------------------

### Prerequisites

-   Python 3.12 or higher

-   [uv](https://github.com/astral-sh/uv "null") (recommended) or pip

-   Git

### 1\. Clone the Repository

```
git clone [https://github.com/23f1000932/Project2-LLM-Analysis-Quiz.git](https://github.com/23f1000932/Project2-LLM-Analysis-Quiz.git)
cd Project2-LLM-Analysis-Quiz

```

### 2\. Configuration (.env)

Create a `.env` file in the root directory. You will need your Google Gemini API key and your specific student/quiz credentials.

```
# .env file
EMAIL=your.email@example.com
SECRET=your_project_secret
GOOGLE_API_KEY=your_gemini_api_key

```

### 3\. Install Dependencies

**Option A: Using `uv` (Recommended)**

```
# Sync project dependencies
uv sync

# Install Playwright browsers (required for scraping)
uv run playwright install chromium

```

**Option B: Using standard `pip`**

```
# Create and activate virtual environment
python -m venv venv
# Windows:
venv\Scripts\activate
# Mac/Linux:
source venv/bin/activate

# Install dependencies
pip install -e .

# Install Playwright browsers
playwright install chromium

```

🚀 How to Run
-------------

### Local Development

Start the FastAPI server. The server will run on port **7860**.

```
# Using uv
uv run main.py

# OR using standard python
python main.py

```

### Docker Deployment

You can build and run the application entirely within Docker, which handles all dependencies including Playwright.

```
# 1. Build the image
docker build -t llm-analysis-agent .

# 2. Run container (passing environment variables)
docker run -p 7860:7860\
  -e EMAIL="your.email@example.com"\
  -e SECRET="your_secret_string"\
  -e GOOGLE_API_KEY="your_api_key"\
  llm-analysis-agent

```

📡 Usage & API Endpoints
------------------------

### 1\. Trigger the Agent (`POST /solve`)

This endpoint accepts the quiz details and starts the autonomous agent in the background.

**Request:**

```
curl -X POST http://localhost:7860/solve\
  -H "Content-Type: application/json"\
  -d '{
    "email": "your.email@example.com",
    "secret": "your_secret_string",
    "url": "[https://tds-llm-analysis.s-anand.net/demo](https://tds-llm-analysis.s-anand.net/demo)"
  }'

```

**Response:**

-   `200 OK`: Agent started successfully.

-   `403 Forbidden`: Invalid secret.

### 2\. Health Check (`GET /healthz`)

Checks if the server is running.

```
curl http://localhost:7860/healthz
# Returns: {"status": "ok", "uptime_seconds": ...}

```

🧰 Tools & Capabilities
-----------------------

The agent has access to the following specialized tools:

1.  **Web Scraper (`get_rendered_html`)**: Uses Playwright to fetch fully rendered HTML, capable of handling dynamic JavaScript content.

2.  **File Downloader (`download_file`)**: Downloads files (CSV, PDF, etc.) to a local `LLMFiles/` directory for processing.

3.  **Code Executor (`run_code`)**: Writes and executes Python code in a subprocess to perform data analysis, ensuring separation from the main application logic.

4.  **Dependency Installer (`add_dependencies`)**: Dynamically installs missing Python packages if the analysis requires specific libraries.

5.  **Answer Submitter (`post_request`)**: Submits the final answer to the quiz API and handles response logic (like retrying or moving to the next question).

📄 License
----------

This project is licensed under the MIT License.
