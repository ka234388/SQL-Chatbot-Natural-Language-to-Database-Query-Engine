# 🤖 SQL Chatbot — Natural Language to Student Database Query Engine

A conversational AI chatbot that lets you ask natural language questions about a Student Management System and get accurate, human-readable answers. Built with **LangChain**, **OpenAI GPT-4o-mini**, and **MySQL**, it translates plain English into SQL, executes it safely, and explains the result.

***

## 🎓 Demo

> **You:** How many students are enrolled in Computer Science?
> **Bot:** There are 42 students currently enrolled in the Computer Science department.

> **You:** Which students have a GPA above 3.5?
> **Bot:** The following students have a GPA above 3.5: Alice Johnson (3.8), Ravi Kumar (3.7), Sara Lee (3.6)...

> **You:** Which course has the most enrollments this semester?
> **Bot:** The course with the most enrollments this semester is "Data Structures" with 87 students enrolled.

***

## 🗂️ Project Structure

```
sql-chatbot/
│
├── SQL_CHATBOT.ipynb       # Main Jupyter Notebook (full pipeline)
├── openai_api.py           # API key config (DO NOT commit to GitHub)
├── requirements.txt        # Python dependencies
├── .gitignore              # Ignores secrets and cache
└── README.md               # Project documentation
```

***

## ⚙️ Tech Stack

| Component        | Tool/Library                        |
|------------------|-------------------------------------|
| LLM              | OpenAI GPT-4o-mini via LangChain    |
| Database         | MySQL (local via PyMySQL)           |
| ORM / DB Utility | LangChain `SQLDatabase`             |
| NL→SQL Chain     | Custom prompt + `ChatOpenAI`        |
| Safety Guard     | Read-only SQL filter (no DML ops)   |
| Interface        | Jupyter Notebook interactive loop   |
| Language         | Python 3.13                         |

***

## 🗄️ Database Schema

The chatbot connects to a MySQL database called `student_db` containing these tables:

### `students`
| Column        | Type    | Description                    |
|---------------|---------|--------------------------------|
| student_id    | INT     | Primary key                    |
| name          | TEXT    | Full name of the student       |
| email         | TEXT    | Student email address          |
| dob           | DATE    | Date of birth                  |
| department_id | INT     | Foreign key → departments      |

### `departments`
| Column        | Type    | Description                    |
|---------------|---------|--------------------------------|
| department_id | INT     | Primary key                    |
| name          | TEXT    | Department name                |
| head_faculty  | TEXT    | Name of department head        |

### `courses`
| Column        | Type    | Description                    |
|---------------|---------|--------------------------------|
| course_id     | INT     | Primary key                    |
| title         | TEXT    | Course title                   |
| credits       | INT     | Number of credit hours         |
| department_id | INT     | Foreign key → departments      |

### `enrollments`
| Column        | Type    | Description                    |
|---------------|---------|--------------------------------|
| student_id    | INT     | Foreign key → students         |
| course_id     | INT     | Foreign key → courses          |
| semester      | TEXT    | e.g., Fall, Spring, Summer     |
| year          | INT     | Academic year                  |

### `grades`
| Column        | Type    | Description                    |
|---------------|---------|--------------------------------|
| student_id    | INT     | Foreign key → students         |
| course_id     | INT     | Foreign key → courses          |
| grade         | TEXT    | Letter grade (A, B, C, etc.)   |
| gpa_points    | DOUBLE  | GPA points for the course      |

***

## 🚀 Setup & Installation

### 1. Prerequisites
- Python 3.9+
- MySQL Server running locally
- OpenAI API key

### 2. Clone the Repository
```bash
git clone https://github.com/YOUR_USERNAME/sql-chatbot.git
cd sql-chatbot
```

### 3. Install Dependencies
```bash
pip install langchain langchain-openai langchain-core langchain-community pymysql
```

Or using the requirements file:
```bash
pip install -r requirements.txt
```

### 4. Configure Your OpenAI API Key

Create a file called `openai_api.py` in the project root:
```python
OPENAI_API_KEY = "sk-your-actual-openai-key-here"
```

> ⚠️ **Never commit this file to GitHub!** It is listed in `.gitignore`.

### 5. Configure MySQL Connection

In the notebook, update the `DATABASE_URI` to match your MySQL credentials:
```python
DATABASE_URI = "mysql+pymysql://YOUR_USER:YOUR_PASSWORD@127.0.0.1:3306/student_db"
```

### 6. Run the Notebook
Open `SQL_CHATBOT.ipynb` in Jupyter and run all cells top-to-bottom.

***

## 💬 How to Use

Once all cells are executed, the chatbot starts an interactive loop in the last cell:

```
🤖 Student Database Chatbot Ready!
Tables: ['students', 'departments', 'courses', 'enrollments', 'grades']
Type 'exit' to quit.

You: ▌
```

**Type any natural language question**, for example:

```
You: List all students in the Computer Science department
You: Which students have a GPA above 3.5?
You: How many students are enrolled in each course?
You: What is the average GPA per department?
You: Which courses have the most enrollments this semester?
You: Show all courses offered by the Engineering department
You: Which student has the highest GPA?
You: How many students are in each department?
```

Type `exit`, `quit`, or `bye` to stop the chatbot.

***

## 🔒 Safety Features

- **Read-Only Guard:** Blocks any `INSERT`, `UPDATE`, `DELETE`, `DROP`, `ALTER`, or `TRUNCATE` queries — student data is always protected.
- **Anti-Hallucination:** The LLM is instructed to answer strictly from database results only — no invented names, grades, or statistics.
- **SQL Cleaner:** Strips markdown fences and rogue prefixes (`SQL:`, `Q:`) from LLM output before execution.

***

## 🧠 How It Works

```
User Question
     │
     ▼
┌─────────────────────────────────┐
│  SQL Generation Prompt          │
│  (Schema + Rules + Examples)    │
│  → GPT-4o-mini                  │
└────────────┬────────────────────┘
             │ Raw SQL
             ▼
        clean_sql()       ← removes markdown/prefixes
             │
        is_safe_sql()     ← blocks DML queries
             │
        db.run(sql)       ← executes on MySQL
             │
             ▼
┌─────────────────────────────────┐
│  Explanation Prompt             │
│  (Result → Natural Language)    │
│  → GPT-4o-mini                  │
└────────────┬────────────────────┘
             │
             ▼
       🤖 Bot Answer
```

***

## 📦 requirements.txt

```
langchain
langchain-openai
langchain-core
langchain-community
pymysql
openai
```

***

## 🙈 .gitignore

```
openai_api.py
__pycache__/
*.pyc
.env
.ipynb_checkpoints/
```

***

## 📌 Notes

- LLM temperature is set to `0.2` for consistent, deterministic query generation.
- Window functions (`COUNT(*) OVER()`) are used to combine count + list results in a single query.
- The prompt includes strict schema rules so the LLM never invents column or table names.
- Designed to be easily extended to any other MySQL database by updating the `DATABASE_URI` and schema prompt.

***

## 👩‍💻 Author

**Karthika Devi**
Graduate Student — Computer Vision & AI
University of Central Florida (UCF)

***
