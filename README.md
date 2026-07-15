# 🤖 Agentic AI Job Search & Intelligence System

An agentic AI system that discovers live job opportunities, analyzes them against a candidate profile, scores and ranks relevant roles, identifies skill gaps, stores structured job intelligence, and sends personalized job recommendations.

> 🔒 **Source code is maintained in a private repository.**
> This public repository showcases the project's architecture, agentic workflow, technical approach, capabilities, and roadmap.
https://github.com/parul001/SQLAgentFetchJobs

---

## 🎯 Project Overview

Traditional job alerts are usually keyword-based: a job matches a search term, so a notification is sent.

This project goes a step further.

I built an **AI-powered job search agent** that not only discovers live opportunities but also evaluates each new job against a candidate profile before deciding whether it is relevant enough to recommend.

The system combines:

**Live Job Data + APIs + Databases + Local LLMs + AI Agents + Scheduled Workflows**

The agent can:

* 🔎 Discover live job opportunities
* 🗄️ Store structured job data
* ♻️ Deduplicate previously discovered jobs
* 🤖 Analyze new jobs using a local LLM
* 🎯 Generate a personalized match score
* ✅ Identify matching skills
* 📚 Identify potential skill gaps
* 💡 Generate an application recommendation
* 🏆 Rank the strongest opportunities
* 📧 Send personalized job recommendations
* 💬 Query accumulated job data using natural language

---

# ✨ Key Capabilities

## 🔎 1. Live Job Discovery

The system retrieves current job listings from the Adzuna Jobs API.

Instead of relying on a single job title, the search pipeline can retrieve opportunities across multiple related roles, such as:

* SDET
* Senior SDET
* Software Development Engineer in Test
* QA Automation Engineer
* Software QA Engineer

The retrieved job data can include:

* Job title
* Company
* Location
* Job description
* Salary information, when available
* Job URL
* Source
* Posted date
* Fetch timestamp

---

## 🗄️ 2. Persistent Job Intelligence Database

Job listings are stored in a local SQLite database.

Each external job ID is stored as a unique identifier, preventing the same opportunity from being repeatedly processed.

```text
Live Jobs
    ↓
Normalize
    ↓
Deduplicate
    ↓
SQLite Database
```

The database stores both the original job information and AI-generated analysis.

Example information stored for an analyzed job:

```text
Job Title
Company
Location
Description
Posted Date

        +

AI Match Score
Matched Skills
Missing Skills
Recommendation
Match Reason
```

This turns the database from a simple collection of job listings into a growing **job intelligence dataset**.

---

## ♻️ 3. Intelligent Deduplication

Before invoking the LLM, the system checks whether a job has already been processed.

```text
Job Retrieved
      ↓
Already in Database?
     /           \
   Yes            No
    ↓              ↓
  Skip       AI Analysis
```

This is important because LLM inference is more expensive than a database lookup.

Previously analyzed jobs are skipped, avoiding:

* Duplicate records
* Repeated LLM analysis
* Repeated recommendations
* Unnecessary processing

---

# 🧠 4. AI-Powered Job Analysis

Each newly discovered job is analyzed by a locally running LLM through Ollama.

The agent evaluates the job against a structured candidate profile.

The profile can include:

* Target roles
* Existing technical skills
* Preferred locations
* Minimum match threshold

Conceptually:

```text
Candidate Profile
        +
Job Description
        ↓
Local LLM Analysis
        ↓
Structured Job Evaluation
```

The AI produces structured analysis such as:

```json
{
  "match_score": 87,
  "matched_skills": [
    "Java",
    "SQL",
    "API Testing"
  ],
  "missing_skills": [
    "Playwright",
    "AWS"
  ],
  "recommendation": "Strong Apply",
  "reason": "Strong alignment with the candidate's automation and API testing experience."
}
```

---

# 🎯 5. Personalized Match Scoring

Every new job receives a match score between:

```text
0 ─────────────────────────────── 100
Poor Match                    Strong Match
```

The analysis considers factors such as:

* Role relevance
* Technical skill alignment
* Experience relevance
* Location preference

Example:

```text
🔥 Match Score: 91%

Senior SDET

Matched Skills:
✓ Java
✓ API Testing
✓ SQL
✓ Test Automation

Potential Skill Gaps:
• Playwright
• AWS

Agent Recommendation:
Strong Apply
```

---

# 📚 6. Skill-Gap Detection

The agent identifies both:

```text
Candidate Skills
      ∩
Job Requirements
      ↓
Matched Skills
```

and:

```text
Job Requirements
      -
Candidate Skills
      ↓
Potential Skill Gaps
```

This allows the system to provide more useful information than a simple job alert.

The user can understand:

* Why a job is relevant
* Which existing skills match
* Which skills may require improvement

---

# 💡 7. AI-Generated Recommendations

The agent classifies each job into a recommendation category:

```text
🔥 Strong Apply
✅ Apply
🤔 Maybe
⏭️ Skip
```

The recommendation is stored with the job for future analysis.

This enables the system to distinguish between:

```text
New Job
```

and:

```text
New + Relevant Job
```

---

# 🏆 8. Intelligent Job Ranking

Jobs that pass the configured match threshold are ranked by their AI-generated match score.

```text
New Jobs
    ↓
AI Analysis
    ↓
Match Scores
    ↓
Filter by Threshold
    ↓
Rank Highest → Lowest
```

Example:

```text
🥇 Senior SDET                 94%
🥈 QA Automation Engineer     88%
🥉 SDET II                    81%
```

Only the strongest opportunities need to reach the notification stage.

---

# 📧 9. Personalized Job Recommendations

Instead of sending every newly discovered job, the system can send a curated digest containing only jobs that meet the configured relevance threshold.

Example:

```text
🤖 Your AI Job Agent Found 3 Strong Matches

🥇 Senior SDET
Match Score: 94%

Matched Skills:
✓ Java
✓ API Testing
✓ SQL

Potential Gaps:
• AWS

Recommendation:
🔥 Strong Apply

[View Job]
```

This reduces notification noise and makes each alert more actionable.

---

# ⏰ 10. Scheduled Job Monitoring

The job discovery pipeline can run automatically on a daily schedule.

```text
Daily Schedule
      ↓
Search Live Jobs
      ↓
Deduplicate
      ↓
Analyze New Jobs
      ↓
Score & Rank
      ↓
Send Recommendations
```

This enables continuous job monitoring without requiring the workflow to be started manually every day.

---

# 💬 11. Natural-Language SQL Agent

The project also includes a LangChain-powered SQL agent connected to the job database.

Instead of manually writing SQL, the user can ask questions in natural language.

### Example Questions

> “Show me the highest-rated SDET jobs.”

> “Which companies have the strongest job matches?”

> “Show jobs with a match score above 80.”

> “Which skills appear most frequently in my skill gaps?”

> “Which locations have the most relevant opportunities?”

> “Show me the latest jobs where my Java experience is relevant.”

The agent translates the user's intent into database operations and returns a human-readable answer.

---

# 🏗️ System Architecture

```text
                    ┌─────────────────────┐
                    │   Candidate Profile │
                    └──────────┬──────────┘
                               │
                               │
                    ┌──────────▼──────────┐
                    │   Job Search Terms  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Adzuna Jobs API   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Job Ingestion Layer │
                    │       Python        │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Normalize &         │
                    │ Deduplicate Jobs    │
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │   Already Known?    │
                    └──────┬────────┬─────┘
                           │        │
                          Yes       No
                           │        │
                           ▼        ▼
                         Skip   ┌──────────────────┐
                                │ Ollama Local LLM │
                                │ Job Analysis     │
                                └────────┬─────────┘
                                         │
                                         ▼
                              ┌──────────────────────┐
                              │ Structured Analysis  │
                              │                      │
                              │ • Match Score        │
                              │ • Matched Skills     │
                              │ • Missing Skills     │
                              │ • Recommendation     │
                              │ • Reason             │
                              └──────────┬───────────┘
                                         │
                                         ▼
                              ┌──────────────────────┐
                              │   SQLite Database    │
                              └───────┬────────┬─────┘
                                      │        │
                         ┌────────────┘        └────────────┐
                         │                                  │
                         ▼                                  ▼
              ┌────────────────────┐             ┌─────────────────┐
              │ Filter & Rank Jobs │             │ LangChain SQL   │
              │ by Match Score     │             │ Agent           │
              └─────────┬──────────┘             └────────┬────────┘
                        │                                 │
                        ▼                                 ▼
              ┌────────────────────┐             ┌─────────────────┐
              │ Personalized Email │             │ Natural Language│
              │ Recommendations    │             │ Job Intelligence│
              └────────────────────┘             └─────────────────┘
```

---

# 🔄 Agent Workflow

```text
START
  ↓
Load Candidate Profile
  ↓
Search Multiple Target Roles
  ↓
Fetch Live Jobs
  ↓
Deduplicate API Results
  ↓
Check Job Database
  ↓
Is Job New?
  │
  ├── No ─────────────────────────→ Skip
  │
  └── Yes
       ↓
  Analyze with Local LLM
       ↓
  Generate Match Score
       ↓
  Identify Matched Skills
       ↓
  Identify Skill Gaps
       ↓
  Generate Recommendation
       ↓
  Store Job + AI Analysis
       ↓
  Does Score Pass Threshold?
       │
       ├── No → Store for Analysis
       │
       └── Yes
            ↓
       Add to Recommendations
            ↓
       Rank by Match Score
            ↓
       Generate Email Digest
            ↓
           END
```

---

# 🛠️ Technology Stack

| Technology    | Purpose                             |
| ------------- | ----------------------------------- |
| Python        | Core agent and data pipeline        |
| Adzuna API    | Live job discovery                  |
| SQLite        | Persistent job intelligence storage |
| SQL           | Structured job analysis             |
| LangChain     | AI and SQL-agent integration        |
| Ollama        | Local LLM inference                 |
| Gmail SMTP    | Personalized job notifications      |
| Cron          | Scheduled execution                 |
| Requests      | REST API communication              |
| python-dotenv | Secure configuration                |
| Git & GitHub  | Version control                     |

---

# 🤖 What Makes This Agentic?

The system is evolving from a fixed job-fetching script toward an agentic workflow.

A traditional pipeline follows predetermined instructions:

```text
Fetch → Store → Email
```

This system introduces AI-driven evaluation:

```text
Discover
    ↓
Observe Job
    ↓
Reason About Relevance
    ↓
Evaluate Skills
    ↓
Generate Match Score
    ↓
Recommend Action
    ↓
Decide Whether to Surface
```

The LLM is responsible for reasoning about:

* Job relevance
* Skill alignment
* Potential skill gaps
* Application recommendations

The surrounding deterministic system handles:

* API communication
* Database persistence
* Deduplication
* Scheduling
* Notification delivery

This hybrid approach uses AI reasoning where judgment is valuable and deterministic code where reliability is more important.

---

# 🧩 Agent Tools & Capabilities

The project currently provides capabilities equivalent to:

```text
Job Search Agent
      │
      ├── Search Live Jobs
      │
      ├── Check Existing Jobs
      │
      ├── Analyze Job Relevance
      │
      ├── Identify Skill Matches
      │
      ├── Detect Skill Gaps
      │
      ├── Score Opportunities
      │
      ├── Rank Recommendations
      │
      ├── Query Job Intelligence
      │
      └── Send Job Digest
```

---

# 🔐 Privacy & Local AI

The project uses Ollama to run the LLM locally.

Benefits include:

* Local AI inference
* No paid LLM API required for job analysis
* Greater control over the model environment
* Ability to experiment with open-source models
* Candidate-profile analysis can remain local

Sensitive credentials are stored using environment variables and excluded from version control.

---

# 📈 Key Engineering Concepts Demonstrated

### 🤖 AI Agent Design

Combining LLM reasoning with deterministic software components.

### 🔌 Live API Integration

Retrieving and processing continuously changing external data.

### 🧱 Structured LLM Output

Converting model analysis into structured fields such as:

* Match score
* Skills
* Recommendations
* Reasoning summaries

### 🗄️ Persistent AI Memory

Storing previous job discoveries and AI analysis in a database.

### ♻️ Cost-Aware Processing

Checking the database before invoking the LLM to avoid repeatedly analyzing the same job.

### 🎯 AI-Based Filtering

Using semantic reasoning rather than only keyword matching.

### 🏆 AI-Assisted Ranking

Prioritizing opportunities based on candidate relevance.

### 💬 Natural-Language Data Interaction

Using an AI SQL agent to explore accumulated job intelligence.

### ⚙️ Hybrid AI Architecture

Combining:

```text
Deterministic Software
        +
LLM Reasoning
```

---

# 📊 Example Agent Output

```text
🤖 Analyzing: Senior SDET

Match Score: 92%

Matched Skills:
✓ Java
✓ API Testing
✓ SQL
✓ Test Automation

Potential Skill Gaps:
• Playwright
• AWS

Recommendation:
🔥 Strong Apply

Reason:
Strong alignment with the candidate's test automation,
API testing, SQL, and software quality engineering experience.
```

---

# 🚀 Future Agentic Improvements

## 🔄 Adaptive Search Planning

Currently, target job searches can be configured in advance.

A future Search Planner Agent could dynamically generate and refine queries.

```text
Candidate Profile
       ↓
Search Planner
       ↓
Generate Search Queries
       ↓
Search Jobs
       ↓
Evaluate Results
       ↓
Enough Relevant Jobs?
    /             \
  Yes              No
   ↓                ↓
Continue       Refine Search
                    ↓
                 Search Again
```

This would introduce an autonomous feedback loop.

---

## 🧠 Multi-Agent Architecture

A future version could separate responsibilities between specialized agents:

```text
             ┌─────────────────────┐
             │   Orchestrator      │
             │      Agent          │
             └──────────┬──────────┘
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ Search Agent │ │ Match Agent  │ │ Career Agent │
└──────────────┘ └──────────────┘ └──────────────┘
        │               │                │
        ▼               ▼                ▼
 Discover Jobs     Score Jobs      Skill Insights
```

---

## 📄 Resume-Aware Matching

Future versions can analyze the candidate's resume directly instead of relying only on a manually configured profile.

```text
Resume
   ↓
Profile Extraction Agent
   ↓
Structured Candidate Profile
   ↓
Job Matching Agent
```

---

## 📚 Market-Level Skill Intelligence

As the database grows, the system can analyze:

* Most requested technical skills
* Emerging testing technologies
* Common candidate skill gaps
* Hiring trends by location
* Role-specific technology demand

Example:

```text
Your Top Recurring Skill Gaps

1. Playwright
2. AWS
3. Docker
4. Kubernetes
5. TypeScript
```

---

## ☁️ Continuous Deployment

Move the agent from a local scheduled process to cloud infrastructure for continuous execution independent of a local machine.

---

# 🗺️ Future Vision

```text
                  Candidate Resume
                         ↓
                 Profile Agent
                         ↓
                 Candidate Profile
                         ↓
                  Search Planner
                         ↓
                Multiple Job Sources
                         ↓
                Job Discovery Agent
                         ↓
                Normalize & Deduplicate
                         ↓
                  Job Match Agent
                         ↓
          ┌──────────────┼──────────────┐
          │              │              │
          ▼              ▼              ▼
     Match Score    Skill Gaps    Recommendation
          │              │              │
          └──────────────┼──────────────┘
                         ↓
                   Job Database
                         ↓
          ┌──────────────┴──────────────┐
          │                             │
          ▼                             ▼
    AI SQL Agent                  Career Agent
          │                             │
          ▼                             ▼
   Job Intelligence              Career Insights
          │                             │
          └──────────────┬──────────────┘
                         ▼
                Personalized Digest
```

---

# 📸 Project Demo

The public showcase can include demonstrations of:

* 🔎 Live job discovery
* 🤖 Local LLM job analysis
* 🎯 AI-generated match scores
* 📚 Matched and missing skills
* 💡 Application recommendations
* 🏆 Ranked job opportunities
* 📧 Personalized email digests
* 💬 Natural-language SQL queries

> Screenshots and demonstrations will be added as the project evolves.

---

# 🔒 Source Code

The complete implementation is maintained in a **private repository**.

This public repository showcases:

* System architecture
* Agentic workflow
* Technical approach
* AI integration
* Engineering decisions
* Current capabilities
* Future roadmap

---

# 👩‍💻 About the Project

This project is part of my hands-on exploration of:

* AI Agents
* Agentic AI
* Generative AI
* Local LLMs
* AI-powered software engineering
* Data pipelines
* Workflow orchestration

The goal is to move beyond basic LLM prompting and build practical AI systems that can:

```text
Observe → Reason → Evaluate → Decide → Act
```

---

# ⭐ Project Status

🟢 **Active Development**

### Current Capabilities

* ✅ Live job discovery
* ✅ Multiple role searches
* ✅ Job deduplication
* ✅ Persistent SQLite storage
* ✅ Local LLM job analysis
* ✅ AI-generated match scoring
* ✅ Matched-skill identification
* ✅ Skill-gap identification
* ✅ Application recommendations
* ✅ Relevance-based filtering
* ✅ Job ranking
* ✅ Personalized email notifications
* ✅ Scheduled execution
* ✅ Natural-language SQL querying

### In Progress / Planned

* 🔄 Adaptive search planning
* 🔄 Search refinement loops
* 🔄 Resume-aware matching
* 🔄 Multi-agent orchestration
* 🔄 Market-level skill analysis
* 🔄 Analytics dashboard
* 🔄 Cloud deployment

---

# 👩‍💻 Author

**Parul Bhargava**

Building and exploring practical applications of **AI Agents, Agentic AI, Generative AI, Local LLMs, and Software Engineering**.
