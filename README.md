# 🤖 SQL Chatbot — Natural Language to MySQL Query Engine

A conversational AI chatbot that lets you ask natural language questions about a MySQL database and get accurate, human-readable answers. Built with **LangChain**, **OpenAI GPT-4o-mini**, and **MySQL**, it translates plain English into SQL, executes it safely, and explains the result.

---

## 🎬 Demo

> **You:** Who acted in KGF Chapter 2?
> **Bot:** The actors in K.G.F: Chapter 2 are Yash and Sanjay Dutt.

> **You:** What is the highest revenue earning movie?
> **Bot:** The highest revenue earning movie is "Pather Panchali" with a revenue of $100,000.

> **You:** Which movies made a profit?
> **Bot:** Movies that made a profit include K.G.F: Chapter 2 with a profit of 11.5 Billion INR...

---

## 🗂️ Project Structure
sql-chatbot/
│
├── SQL_CHATBOT.ipynb # Main Jupyter Notebook (full pipeline)
├── openai_api.py # API key config (DO NOT commit to GitHub)
├── requirements.txt # Python dependencies
├── .gitignore # Ignores secrets and cache
└── README.md # Project documentation



---

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

---

## 🗄️ Database Schema

The chatbot connects to a MySQL database called `new_schema` containing these tables:

### `movies-db-1`
| Column       | Type    | Description               |
|--------------|---------|---------------------------|
| movie_id     | INT     | Primary key               |
| title        | TEXT    | Movie title               |
| industry     | TEXT    | Bollywood / Hollywood     |
| release_year | INT     | Year of release           |
| imdb_rating  | DOUBLE  | IMDb score                |
| studio       | TEXT    | Production studio         |
| language_id  | INT     | Foreign key → languages   |

### `actors`
| Column    | Type | Description     |
|-----------|------|-----------------|
| actor_id  | INT  | Primary key     |
| name      | TEXT | Actor name      |
| birth_year| INT  | Year of birth   |

### `financials`
| Column   | Type   | Description              |
|----------|--------|--------------------------|
| movie_id | INT    | Foreign key → movies     |
| budget   | INT    | Production budget        |
| revenue  | DOUBLE | Box office revenue       |
| unit     | TEXT   | Millions / Billions      |
| currency | TEXT   | USD / INR                |

### `languages`
| Column      | Type | Description   |
|-------------|------|---------------|
| language_id | INT  | Primary key   |
| name        | TEXT | Language name |

### `movie_actor`
| Column   | Type | Description                    |
|----------|------|--------------------------------|
| movie_id | INT  | Foreign key → movies           |
| actor_id | INT  | Foreign key → actors           |

---

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
DATABASE_URI = "mysql+pymysql://YOUR_USER:YOUR_PASSWORD@127.0.0.1:3306/YOUR_DB_NAME"
```

### 6. Run the Notebook
Open `SQL_CHATBOT.ipynb` in Jupyter and run all cells top-to-bottom.

---

## 💬 How to Use

Once all cells are executed, the chatbot starts an interactive loop in the last cell:
🤖 Movie Database Chatbot Ready!
Tables: ['actors', 'financials', 'languages', 'movie_actor', 'movies-db-1']
Type 'exit' to quit.


**Type any natural language question**, for example:
You: Show all Bollywood movies
You: Top 5 highest rated movies
You: Which movies made a profit?
You: Who acted in KGF Chapter 2?
You: What language is Pather Panchali in?
You: What is the budget and revenue of the latest movie?
You: Which actor has acted in the most movies?
You: Movies released between 2015 and 2022


Type `exit`, `quit`, or `bye` to stop the chatbot.

---

## 🔒 Safety Features

- **Read-Only Guard:** Blocks any `INSERT`, `UPDATE`, `DELETE`, `DROP`, `ALTER`, or `TRUNCATE` queries.
- **Anti-Hallucination:** The LLM is instructed to answer strictly from database results only.
- **SQL Cleaner:** Strips markdown fences and rogue prefixes (`SQL:`, `Q:`) from LLM output before execution.

---

## 🧠 How It Works
User Question
│
▼
┌─────────────────────────────────┐
│ SQL Generation Prompt │
│ (Schema + Rules + Examples) │
│ → GPT-4o-mini │
└────────────┬────────────────────┘
│ Raw SQL
▼
clean_sql() ← removes markdown/prefixes
│
is_safe_sql() ← blocks DML queries
│
db.run(sql) ← executes on MySQL
│
▼
┌─────────────────────────────────┐
│ Explanation Prompt │
│ (Result → Natural Language) │
│ → GPT-4o-mini │
└────────────┬────────────────────┘
│
▼
🤖 Bot Answer

---

## 📦 requirements.txt
langchain
langchain-openai
langchain-core
langchain-community
pymysql
openai


---

## 🙈 .gitignore
openai_api.py
__pycache__/
*.pyc
.env
.ipynb_checkpoints/


---

## 📌 Notes

- The movies table is named `movies-db-1` — always referenced with backticks in SQL.
- The LLM temperature is set to `0.2` for consistent, deterministic query generation.
- Window functions (`COUNT(*) OVER()`) are used to combine count + list in a single query.

---

## 👩‍💻 Author

**Karthika Ramasamy**  
Graduate Student — Computer Vision & AI  
University of Central Florida (UCF)

