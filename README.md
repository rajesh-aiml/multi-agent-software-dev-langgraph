# Multi-Agent Software Development System

A multi-agent AI system built with **LangGraph** and **Google Gemini 2.5 Flash**. Give it a programming task and it plans the solution, writes tests, writes the code, runs the tests, reviews the code, and produces a final report. If the tests fail or the reviewer rejects the code, it fixes the code and tries again.

## Features

- 6 specialized agents working in a LangGraph workflow
- Test-driven approach: tests are written before the code
- Automatic retry loop with a maximum number of attempts
- Structured code review using Pydantic
- Optional Docker sandbox for running generated code safely
- Optional human approval before each code fix
- Streaming output of every agent's result
- Final report with status, history, and final code

## Agents

| Agent | Role |
|-------|------|
| Planner | Writes a short plan (max 5 lines) with exact function names and signatures |
| Test Writer | Writes 3 to 5 `assert` based tests, including edge cases |
| Coder | Writes the solution, or fixes it using test errors and reviewer feedback |
| Tester | Saves the code and tests in a temp folder and runs them (locally or in Docker) |
| Reviewer | Checks correctness, cleanliness and safety, and returns approval and feedback |
| Reporter | Generates the final report |

## Workflow

```
Planner -> Test Writer -> Coder -> Tester --(passed)--> Reviewer --(approved)--> Report -> END
                            ^         |                     |
                            |--(failed)                     |
                            |----------(rejected)-----------|
```

- If tests fail, the flow goes back to the Coder.
- If the reviewer rejects the code, the flow goes back to the Coder.
- After `MAX_TRIES` attempts, the flow goes to the final report.

## Requirements

- Python 3.10 or higher
- A Google Gemini API key
- Docker (optional, only if `USE_DOCKER = True`)

## Installation

```bash
git clone https://github.com/rajesh-aiml/multi-agent-software-dev-langgraph
.git
cd multi-agent-software-dev-langgraph

pip install langgraph langchain-google-genai pydantic
```

## Getting the API Key

1. Go to [Google AI Studio](https://aistudio.google.com/)
2. Click **API keys**
3. Click **Create API key** and copy it
4. Paste it in the code here:

```python
os.environ["GOOGLE_API_KEY"] = "google_api_key_here"
```

## Usage

```bash
python app.py
```

To change the task, edit this line in `initial_input`:

```python
"task": "Write a Python function chunk_text to split text into chunks of a given size"
```

## Configuration

| Setting | Default | Description |
|---------|---------|-------------|
| `MAX_TRIES` | `3` | Maximum number of coding attempts |
| `USE_DOCKER` | `False` | Run generated code inside a Docker container with no network |
| `USE_HUMAN_APPROVAL` | `False` | Pause before the Coder node so you can review and press Enter to continue |

## Sample Output
<img width="1673" height="501" alt="Screenshot 2026-11-25 182813" src="https://github.com/user-attachments/assets/e64419ba-cd66-4ef3-8c29-65eba74f542d" />
<img width="1668" height="608" alt="Screenshot 2026-11-25 182838" src="https://github.com/user-attachments/assets/a193dc0f-7dc8-474e-ae98-07479132d8fb" />
<img width="1623" height="638" alt="Screenshot 2026-11-25 182908" src="https://github.com/user-attachments/assets/affa7698-a796-46c2-9ca1-fdffb399c23d" />



## Safety Note

By default, the AI-generated code runs directly on your computer. For safer execution, set `USE_DOCKER = True` so the code runs in an isolated container.
and if USE_HUMAN_APPROVAL is True. then the code will interrupt and ask for human approval, that time press enter so the code will run from where it stopped.again it wont run from starting it self

## Tech Stack

- Python
- LangGraph
- LangChain Google GenAI
- Google Gemini 2.5 Flash
- Pydantic
