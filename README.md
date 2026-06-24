# Resume Builder and Analyzer

**A Flask web app that builds AI-written resumes and ranks PDF resumes against a job description with semantic matching.**

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white) ![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white) ![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat&logo=mongodb&logoColor=white) ![Google Gemini](https://img.shields.io/badge/Gemini%202.0%20Flash-8E75B2?style=flat&logo=googlegemini&logoColor=white) ![Hugging Face](https://img.shields.io/badge/Sentence--BERT-FFD21E?style=flat&logo=huggingface&logoColor=black) ![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=flat&logo=pytorch&logoColor=white) ![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)

## Overview

This is two tools wrapped in one Flask app. The first half takes the details you'd normally type into a resume — name, role, skills, projects, work history, internships, education — and uses Google Gemini to write the prose for you (the "About Me" summary plus per-item paragraphs for each project, job, and internship), then drops everything into one of three HTML resume templates. The second half is an ATS-style ranker: a recruiter pastes a job description, uploads a stack of PDF resumes, and the app scores each one by how closely it matches the description, sorts them best-to-worst, and writes a short plain-language note on what each candidate is missing.

I built it with a college team (Team ISS057, "Deep Thinkers"). It's a functional full-stack project: writing on the generation side is handled by Gemini, matching on the analysis side is handled by a Sentence-BERT embedding model with cosine similarity, and a small Gemini-backed chatbot answers resume questions inside the UI. The whole backend lives in a single `App.py`; the front end is a set of self-contained HTML pages.

## Key Features

- **AI resume builder** — collects personal details, skills, certifications, projects, work experience, internships, education, and languages through a form, then generates the written sections with Gemini.
- **Gemini-written summaries** — an ATS-friendly "About Me" paragraph plus a separate 3–4 line professional summary for every project, job, and internship, each prompted from the title, tech, company, and responsibilities you provide.
- **Three resume templates** — pick `resume1`, `resume2`, or `resume3`; the app renders the same data into the chosen Jinja2 template (`Resume_Template1/2/3.html`).
- **Profile photo handling** — accepts a base64 image from the form, opens it with Pillow, converts to RGB JPEG, and re-encodes it for embedding in the rendered resume.
- **ATS resume analyzer / ranker** — upload one or many PDF resumes plus a job description; the app returns each resume's match score (0–100) sorted highest first.
- **Semantic matching** — resume and job-description text are embedded with Sentence-BERT (`all-MiniLM-L6-v2`) and compared with cosine similarity, so it matches on meaning rather than exact word overlap.
- **Friendly per-resume feedback** — for each resume it reports keyword match percentage, the words present in the job description but missing from the resume, a few keywords already matched, the model's overall fit verdict, and a note on whether the resume is too short or too long.
- **In-memory PDF parsing** — uploaded PDFs are read with `pdfplumber` straight from the request stream; nothing is written to disk.
- **User authentication** — register/login endpoints with bcrypt password hashing and a regex policy (min 8 chars, upper, lower, digit, special character). A default `user` / `User@123` account is seeded on first run if the users collection is empty.
- **Resume-assistant chatbot** — a `/chat` endpoint that forwards questions to Gemini with a resume-assistant system prompt and returns plain text.
- **Animated front end** — three standalone HTML pages (home, login, ATS analyzer) with gradient-animated CSS, Font Awesome icons, and the Poppins font.

## How It Works

The backend is one Flask file, `App.py`. It loads the Sentence-BERT model once at startup, configures Gemini, connects to a local MongoDB, and exposes a handful of JSON/form endpoints. CORS is enabled app-wide so the HTML front end can call it.

### Resume generation — `POST /resume`

The request body is JSON carrying all the form fields plus a `template` name and an optional base64 `image`. The handler:

1. Pulls each field out of the JSON (`name`, `role`, `skills`, `projects`, `workExperience`, `internships`, `education`, etc.).
2. If an image is present, decodes the base64, opens it with Pillow, converts to RGB, re-saves as JPEG, and re-encodes to a `data:image/jpeg;base64,...` string.
3. Calls Gemini for the **About Me** paragraph, prompting for a 3–5 line ATS-friendly summary that works the listed skills in naturally.
4. Loops over projects, work experience, and internships, sending a separate Gemini prompt for each so every entry gets its own 3–4 line first-person summary built from its title/company/tech/responsibilities.
5. Zips the generated summaries back onto their source entries, stores the full record in MongoDB (`User.users`), and renders the chosen template (`Resume_Template1/2/3.html`) with all the data.

Gemini runs as `gemini-2.0-flash-exp` with temperature 1, top-p 0.95, top-k 40, and an 8192-token output cap. Each summary is a fresh `start_chat().send_message(...)` call.

### Resume analysis — `POST /upload/`

This endpoint takes a multipart form with a `job_description` field and a `resumes` file list. It validates that a job description is present and that every uploaded file ends in `.pdf`, then hands the batch to the ranker.

- **Text extraction** — `extract_text_from_pdf` reads each upload into `io.BytesIO` and pulls text page-by-page with `pdfplumber`, skipping any resume that yields no text.
- **Embedding + scoring** — `rank_resumes` encodes the job description once, then encodes each resume and computes cosine similarity with `util.pytorch_cos_sim`. The similarity score becomes that resume's score.
- **Ranking** — resumes are sorted by score descending and returned as `{resume, score (0–100, rounded), feedback}`.

### Feedback generation

`generate_feedback` combines the embedding score with simple keyword bookkeeping. It splits both texts into word sets, computes the intersection and difference, and derives a keyword match percentage. From there it assembles a message covering: how well the keywords line up, which job words are missing, a few words already matched, what the deeper similarity score implies about fit, and whether the resume length (under 100 / over 500 words) needs adjusting. The tone is deliberately encouraging rather than clinical.

### Auth and chatbot

`/api/register` and `/api/login` work against a separate `Login.users` collection. Registration enforces the password regex and stores a bcrypt hash; login verifies with `bcrypt.checkpw`. `/chat` sends the user's message to Gemini with a resume-assistant system prompt, with a hardcoded reply for "who made/trained/created you" questions that credits Team ISS057.

## Tech Stack

- **Language:** Python (backend), HTML/CSS/JS (front end)
- **Framework:** Flask 3.0, Jinja2 templating, Flask-CORS
- **AI / NLP:** Google Gemini (`google-generativeai`, model `gemini-2.0-flash-exp`); Sentence-Transformers `all-MiniLM-L6-v2` on PyTorch + Hugging Face Transformers
- **Data / parsing:** MongoDB via PyMongo; `pdfplumber` for PDF text; Pillow + base64 for images
- **Auth:** bcrypt password hashing with a regex password policy
- **Front end:** standalone HTML pages with animated CSS, Font Awesome, Poppins

## Getting Started

### Prerequisites

- Python 3.8+
- A running MongoDB instance on `mongodb://localhost:27017/`
- A Google Gemini API key

### Installation

```bash
git clone https://github.com/DCode-v05/Resume-Builder-and-Analyzer.git
cd Resume-Builder-and-Analyzer
pip install -r requirements.txt
```

`requirements.txt` pins Flask 3.0.3, google-generativeai 0.7.2, pymongo 4.6.2, sentence-transformers 2.5.1, torch 2.2.1, transformers 4.38.2, pdfplumber 0.10.4, Pillow 10.2.0, bcrypt 4.1.2, and flask-cors 4.0.0.

### Configuration

`App.py` sets the Gemini key inline (`os.environ["GEMINI_API_KEY"] = "Your_Gemini_API_Key"`). Replace that with your own key — and prefer reading it from an environment variable or `.env` rather than committing it. The Mongo connection string is also set inline and points at localhost by default.

### Running

```bash
python App.py
```

Flask starts in debug mode. On first run, if the users collection is empty it seeds a default account (`user` / `User@123`) so you can log in immediately.

## Usage

**Build a resume:** open the app, log in (or register), fill in your details, optionally upload a photo, pick one of the three templates, and submit. Gemini writes the summaries and the rendered HTML resume comes back.

**Analyze resumes:** go to the ATS analyzer page, paste a job description, upload one or more PDF resumes, and submit. You get each resume's match score out of 100 — ranked best to worst — with a short note on missing keywords, matched keywords, overall fit, and length. The `sample resumes/` folder has five PDFs you can test with right away.

## Project Structure

```
Resume-Builder-and-Analyzer/
├── App.py                     # Whole Flask backend: routes, Gemini calls, ATS ranking, auth, chatbot
├── requirements.txt           # Pinned Python dependencies
├── home.html                  # Landing / resume-builder page (animated gradient UI)
├── login.html                 # Login + register page
├── Resume_Details.html        # Resume input form
├── Resume_ATS.html            # ATS analyzer: job description + PDF upload UI
├── logo.png                   # ISS057 logo
├── sample resumes/            # Five sample PDFs (C1061…C1164) for testing the analyzer
└── templates/
    ├── Resume_Template1.html  # Resume layout 1
    ├── Resume_Template2.html  # Resume layout 2
    ├── Resume_Template3.html  # Resume layout 3
    └── Resume1–3.png          # Template preview images
```

---

## Contact

<table>
  <tr><td><b>Portfolio:</b> <a href="https://www.denistan.me">Denistan</a></td><td><b>LinkedIn:</b> <a href="https://www.linkedin.com/in/denistanb">denistanb</a></td></tr>
  <tr><td><b>GitHub:</b> <a href="https://github.com/DCode-v05">DCode-v05</a></td><td><b>LeetCode:</b> <a href="https://leetcode.com/u/Denistan_B">Denistan_B</a></td></tr>
  <tr><td colspan="2" align="center"><b>Email:</b> <a href="mailto:denistanb05@gmail.com">denistanb05@gmail.com</a></td></tr>
</table>

Made with ❤️ by **Denistan B**
