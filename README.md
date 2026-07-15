# 🤖 AI-Powered Live Job Search & SQL Agent

A personal AI engineering project that automates live job discovery, stores and analyzes job data, sends notifications for newly discovered opportunities, and enables natural-language querying using a local LLM.

> 🔒 **Source code is maintained in a private repository.**
> This public repository showcases the project's architecture, features, technology stack, workflow, and results.
https://github.com/parul001/SQLAgentFetchJobs

---

## 🎯 Project Overview

Searching for relevant job opportunities across continuously changing listings can be repetitive and time-consuming.

I built an automated job intelligence pipeline that:

* Fetches live job listings from a job-search API
* Stores structured job data in a local SQL database
* Prevents duplicate job records
* Detects newly discovered opportunities
* Sends automated email notifications
* Refreshes job data on a daily schedule
* Uses an AI-powered SQL agent to query the database using natural language
* Runs the LLM locally using Ollama

The goal was to combine **API integration, data engineering, automation, SQL, and AI agents** into a practical end-to-end project.

---

## ✨ Key Features

### 🔎 Live Job Collection

The system retrieves live job listings through the Adzuna Jobs API.

Job data includes information such as:

* Job title
* Company
* Location
* Description
* Salary information, when available
* Job URL
* Source
* Posted date
* Fetch timestamp

---

### 🗄️ Structured Job Database

Job listings are stored in a local SQLite database.

Each external job ID is stored as a unique identifier, allowing the system to automatically prevent duplicate records.

```text
Live Jobs
    ↓
Fetch
    ↓
Normalize
    ↓
Deduplicate
    ↓
SQLite Database
```

---

### 🆕 New Job Detection

Every time the pipeline runs, fetched jobs are compared against existing database records.

```text
Job Retrieved
     ↓
Already Exists?
   /            \
 Yes             No
  ↓               ↓
Ignore       Save to Database
                  ↓
           Mark as New Job
```

Only newly discovered jobs are included in the notification workflow.

---

### 📧 Automated Email Notifications

When new jobs are discovered, the system automatically sends an email summary.

The notification contains relevant information such as:

* Job title
* Company
* Location
* Direct job link

If no new jobs are found, unnecessary emails are skipped.

```text
New Jobs Found?
     /       \
   Yes        No
    ↓          ↓
Send Email   No Email
```

---

### ⏰ Daily Automation

The job-fetching pipeline is scheduled to run automatically using a cron job.

```text
Daily Schedule
      ↓
Job Fetch Script
      ↓
Live Job API
      ↓
Database Update
      ↓
New Job Detection
      ↓
Email Notification
```

This turns the project into an automated job-monitoring workflow rather than a manually executed script.

---

## 🧠 AI-Powered SQL Agent

The project includes an AI-powered SQL agent built using LangChain and a locally running LLM through Ollama.

Instead of manually writing SQL queries, the user can ask questions in natural language.

### Example Questions

> “Show me the latest SDET jobs.”

> “Which companies are hiring Software QA Engineers?”

> “Show jobs located in Bengaluru.”

> “Which jobs mention Python in their descriptions?”

> “What are the most common skills mentioned across the available jobs?”

The AI agent interprets the question, interacts with the SQL database, and returns a human-readable response.

---

## 🏗️ System Architecture

```text
                 ┌─────────────────────┐
                 │   Adzuna Jobs API   │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Job Ingestion Layer │
                 │      Python         │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Normalize &         │
                 │ Deduplicate Jobs    │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │   SQLite Database   │
                 └───────┬─────┬───────┘
                         │     │
              ┌──────────┘     └──────────┐
              │                           │
              ▼                           ▼
     ┌─────────────────┐        ┌──────────────────┐
     │ New Job         │        │ LangChain SQL    │
     │ Detection       │        │ Agent            │
     └────────┬────────┘        └────────┬─────────┘
              │                          │
              ▼                          ▼
     ┌─────────────────┐        ┌──────────────────┐
     │ Gmail Email     │        │ Ollama Local LLM │
     │ Notification    │        └────────┬─────────┘
     └─────────────────┘                 │
                                        ▼
                               ┌──────────────────┐
                               │ Natural Language │
                               │ Answers          │
                               └──────────────────┘
```

---

## 🔄 End-to-End Workflow

```text
1. Scheduled job starts
        ↓
2. Fetch live job listings
        ↓
3. Parse and normalize job data
        ↓
4. Compare jobs with existing database records
        ↓
5. Store newly discovered jobs
        ↓
6. Send email notification for new opportunities
        ↓
7. Query accumulated job data using an AI SQL agent
```

---

## 🛠️ Technology Stack

| Technology    | Purpose                                |
| ------------- | -------------------------------------- |
| Python        | Core application and data pipeline     |
| Adzuna API    | Live job data                          |
| SQLite        | Local structured job storage           |
| SQL           | Job data querying and analysis         |
| LangChain     | AI agent and database integration      |
| Ollama        | Running the LLM locally                |
| Gmail SMTP    | Automated email notifications          |
| Cron          | Daily workflow scheduling              |
| Requests      | REST API communication                 |
| python-dotenv | Environment variable management        |
| Git & GitHub  | Version control and project management |

---

## 🤖 Why Ollama?

The AI component runs using a local LLM through Ollama.

This provides several advantages:

* Local model execution
* No dependency on a paid LLM API for inference
* Greater control over the model environment
* Useful experimentation with open-source models
* Easy integration with LangChain

The architecture separates the AI model from the underlying data pipeline, making it possible to experiment with different local models.

---

## 🔐 Security & Secret Management

Sensitive information is kept outside the source code using environment variables.

Examples include:

* Job API credentials
* Email credentials
* Application secrets

Secrets are stored locally and excluded from version control.

The project follows the principle:

```text
Source Code → Version Controlled

Secrets → Environment Variables

Local Database → Not Publicly Committed
```

---

## 💡 Key Engineering Concepts Demonstrated

This project combines several software engineering and AI concepts:

### API Integration

Consuming and processing live data from an external REST API.

### ETL-Style Data Pipeline

Fetching, transforming, normalizing, and storing external job data.

### Database Design

Creating a structured schema for persistent job storage.

### Data Deduplication

Using unique external identifiers to prevent duplicate records.

### Automation

Scheduling the pipeline to run without manual intervention.

### Event-Based Notifications

Sending notifications only when new data is discovered.

### AI Agents

Using an LLM to interact with tools and structured data.

### Natural-Language-to-SQL

Allowing users to analyze database information without manually writing SQL.

### Local LLM Integration

Running AI inference locally through Ollama.

### Secure Configuration

Keeping API keys and credentials outside the source code.

---

## 🧪 Example Use Cases

### Job Discovery

```text
Find the latest SDET opportunities.
```

### Company Analysis

```text
Which companies currently have the most relevant openings?
```

### Location Analysis

```text
Which cities have the highest number of available jobs?
```

### Skill Analysis

```text
Which technical skills appear most frequently in job descriptions?
```

### Job Market Exploration

```text
Compare the number of SDET and Software QA Engineer opportunities.
```

---

## 📈 What I Learned

Building this project provided hands-on experience with:

* Designing an end-to-end AI application
* Integrating external APIs with Python
* Building a persistent data ingestion pipeline
* Working with SQLite and SQL
* Handling duplicate data
* Automating workflows using cron
* Sending programmatic email notifications
* Integrating LangChain with structured databases
* Running LLMs locally using Ollama
* Managing Python virtual environments and dependencies
* Debugging package compatibility issues
* Managing secrets securely
* Using Git and GitHub for version control

---

## 🚀 Future Improvements

The project can be extended with several additional capabilities:

### 🎯 Intelligent Job Matching

Compare job descriptions with a candidate's resume and generate a relevance score.

```text
Resume + Job Description
          ↓
     AI Analysis
          ↓
      Match Score
```

### 📊 Skill Gap Analysis

Identify skills frequently requested by employers that are missing from the candidate's profile.

### 🏆 Job Ranking

Automatically rank opportunities based on:

* Skills
* Experience
* Location
* Role relevance
* Technology stack

### 📄 Resume-to-Job Matching

Use embeddings or LLM-based analysis to compare a resume with each new job.

### 🌐 Multiple Job Sources

Extend the ingestion pipeline to normalize job listings from multiple APIs.

```text
Job API A ──┐
Job API B ──┼──→ Normalize → Unified Job Database
Job API C ──┘
```

### 📊 Analytics Dashboard

Build a dashboard showing:

* Jobs discovered over time
* Top hiring companies
* Most requested skills
* Job distribution by location
* Role trends

### ☁️ Cloud Deployment

Move the scheduled pipeline from a local machine to cloud infrastructure for continuous execution.

---

## 🗺️ Future Vision

```text
Multiple Live Job Sources
          ↓
   Data Ingestion Layer
          ↓
 Normalize & Deduplicate
          ↓
    Unified Job Database
          ↓
    ┌─────┴───────────┐
    │                 │
    ▼                 ▼
AI SQL Agent    Resume Matching
    │                 │
    ▼                 ▼
Job Analytics    Match Scores
    │                 │
    └────────┬────────┘
             ▼
     Personalized Ranking
             ↓
      Daily Job Digest
             ↓
       Email / Dashboard
```

---

## 📸 Project Demo

Screenshots and demonstrations can include:

* Live job API fetch results
* New-job detection output
* Automated email notifications
* Natural-language SQL agent queries
* AI-generated database responses

> Screenshots will be added as the project evolves.

---

## 🔒 Source Code

The complete implementation is maintained in a **private repository**.

This public repository is intended to showcase:

* The problem being solved
* System architecture
* Technical approach
* AI integration
* Engineering concepts
* Project outcomes
* Future roadmap

---

## 👩‍💻 About the Project

This project was built as part of my hands-on learning journey in **AI Agents, Generative AI, automation, and AI-powered software engineering**.

My goal was to go beyond basic LLM prompting and build a practical system combining:

**Live Data + APIs + Databases + Automation + AI Agents + Local LLMs**

---

## ⭐ Project Status

🟢 **Active Development**

Current capabilities:

* ✅ Live job ingestion
* ✅ SQLite storage
* ✅ Job deduplication
* ✅ New-job detection
* ✅ Automated email notifications
* ✅ Daily scheduling
* ✅ LangChain SQL agent
* ✅ Local LLM integration with Ollama

Planned:

* 🔄 Resume matching
* 🔄 Skill-gap analysis
* 🔄 AI-powered job ranking
* 🔄 Analytics dashboard
* 🔄 Cloud deployment

---

## 👩‍💻 Author

**Parul Bhargava**

Building and exploring practical applications of **AI Agents, Generative AI, Automation, and Software Engineering**.
