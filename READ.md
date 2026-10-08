# Agentic SQL Engine

## Project Overview

Agentic SQL Engine is a Streamlit-based conversational application that allows users to ask natural-language questions about a SQL database. The application uses LangChain to create an SQL agent, SQLAlchemy to connect to databases, and Groq's Llama 3 model to interpret questions and generate database-oriented responses.

The project supports:

- A local SQLite database containing student records.
- A MySQL database configured through the Streamlit sidebar.
- Natural-language database questions through a chat interface.
- Streaming agent activity displayed in the Streamlit interface.
- Conversation history maintained during the active Streamlit session.

## Main Technologies

- **Python** — Application language.
- **Streamlit** — Web user interface and session state.
- **LangChain** — SQL agent and database toolkit integration.
- **SQLAlchemy** — Database engine and connection abstraction.
- **Groq** — Large language model provider.
- **Llama 3 8B 8192** — Configured language model.
- **SQLite** — Bundled local database.
- **MySQL** — Optional external database connection.

## Application Architecture

The application flow is:

1. Streamlit initializes the page and renders the database selection controls.
2. The user selects either the local SQLite database or a MySQL database.
3. The user provides a Groq API key through the sidebar.
4. The selected database is wrapped in a LangChain `SQLDatabase`.
5. A `SQLDatabaseToolkit` exposes database tools to the agent.
6. LangChain creates a zero-shot SQL agent using the configured Groq model.
7. The user submits a natural-language question in the chat input.
8. The SQL agent interprets the question, interacts with the selected database, and generates a response.
9. The user question and agent response are stored in Streamlit session state and shown in the chat history.

## Project Files

| File | Description |
| --- | --- |
| `app.py` | Main Streamlit application. It configures the interface, database connection, Groq model, SQL toolkit, SQL agent, and chat workflow. |
| `sqlite.py` | SQLite database setup script. It creates the `STUDENT` table, inserts sample records, prints the records, and commits the database changes. |
| `student.db` | SQLite database file used by the local database option. |
| `requirements.txt` | Python dependency list for the LangChain, database, model, data-processing, and supporting integrations used by the project. |
| `READ.md` | Project documentation. |

## Database Options

### Local SQLite Database

The local option uses the `student.db` file located in the same directory as `app.py`. The application builds an absolute path to this file and opens it in read-only mode through SQLite and SQLAlchemy.

The local database contains one table:

```sql
CREATE TABLE STUDENT(
    NAME VARCHAR(25),
    CLASS VARCHAR(25),
    SECTION VARCHAR(25),
    MARKS INT
);
```

The bundled sample records are:

| Name | Class | Section | Marks |
| --- | --- | --- | ---: |
| Krish | Data Science | A | 90 |
| John | Data Science | B | 100 |
| Mukesh | Data Science | A | 86 |
| Jacob | DEVOPS | A | 50 |
| Dipesh | DEVOPS | A | 35 |

### MySQL Database

The MySQL option collects the following values in the sidebar:

- MySQL host
- MySQL user
- MySQL password
- MySQL database name

The application uses the SQLAlchemy URL format below to create the connection:

```text
mysql+mysqlconnector://<user>:<password>@<host>/<database>
```

## Requirements

The project uses the dependencies listed in `requirements.txt`, including:

- `langchain`
- `langchain-community`
- `langchain-groq`
- `SQLAlchemy`
- `mysql-connector-python`
- `python-dotenv`
- `pandas`
- `openai`
- `chromadb`
- `faiss-cpu`
- `sentence_transformers`
- `huggingface_hub`
- Additional document, search, embedding, and utility integrations

The application interface is run with Streamlit.

## Setup

### 1. Create and activate a virtual environment

Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 2. Install dependencies

```powershell
pip install -r requirements.txt
pip install streamlit
```

### 3. Prepare the local database

The repository already contains `student.db`. To recreate the database from the setup script, run:

```powershell
python sqlite.py
```

The script creates the `STUDENT` table and inserts the sample student records.

### 4. Start the application

```powershell
streamlit run app.py
```

Streamlit displays a local URL in the terminal. Open that URL in a browser to use the application.

## Configuration

### Groq API Key

Enter the Groq API key in the **GRoq API Key** field in the Streamlit sidebar. The application passes this key to `ChatGroq`.

The configured model is:

```text
Llama3-8b-8192
```

The model is initialized with streaming enabled.

### Local Database Mode

Select:

```text
Use SQLLite 3 Database- Student.db
```

This connects the SQL agent to the bundled SQLite database.

### MySQL Mode

Select:

```text
Connect to you MySQL Database
```

Then provide the MySQL host, user, password, and database name in the sidebar.

## User Interface

The application provides:

- A page title and application heading.
- A sidebar database selector.
- Database-specific connection fields.
- A password input for the Groq API key.
- A **Clear message history** button.
- A chat display for previous messages.
- A chat input for natural-language questions.
- Streamed agent callbacks displayed while the SQL agent is processing a request.

The initial assistant message is:

```text
How can I help you?
```

## Agent Configuration

The SQL agent is created with:

- The Groq chat model.
- A LangChain `SQLDatabaseToolkit`.
- The selected database.
- `AgentType.ZERO_SHOT_REACT_DESCRIPTION`.
- Verbose execution enabled.

The database configuration function is cached with Streamlit's resource cache for two hours:

```text
@st.cache_resource(ttl="2h")
```

## Example Questions

After connecting to the local database, users can ask questions such as:

- How many students are in the database?
- Show all students in Data Science.
- Who scored the highest marks?
- What is the average mark for DEVOPS?
- List students from section A.

The agent converts the natural-language request into database operations and returns the result in the chat interface.

## Database Initialization Script

`sqlite.py` performs the following operations:

1. Opens a connection to `student.db`.
2. Creates a SQLite cursor.
3. Creates the `STUDENT` table.
4. Inserts five sample records.
5. Queries and prints all inserted records.
6. Commits the changes.
7. Closes the database connection.

## Runtime Session Behavior

Streamlit session state stores messages under the `messages` key. When a new session begins, the message list is initialized with the assistant greeting. Selecting **Clear message history** resets the list to that initial greeting.

Each user request and generated assistant response is appended to the same session message list and rendered using Streamlit chat messages.

