# Resume Builder and Analyzer

A Flask app with two tools. One writes a resume from the details you enter, using Gemini for the prose and one of three HTML templates for the layout. The other ranks uploaded PDF resumes against a job description.

It was a team project (Team ISS057, "Deep Thinkers"). The backend is a single file, `App.py`.

## How it works

**Resume builder (`POST /resume`)**

The request carries the form fields (name, role, skills, projects, work experience, internships, education and so on), a `template` name (`resume1`, `resume2` or `resume3`) and an optional base64 photo. The handler converts the photo to JPEG with Pillow, then asks Gemini (`gemini-2.0-flash-exp`) for an "About Me" paragraph and a short first-person summary for each project, job and internship. The record is saved to MongoDB (`User.users`) and rendered through `templates/Resume_Template1.html`, `2` or `3`.

**Resume analyzer (`POST /upload/`)**

Takes a `job_description` and a list of PDFs. Text is extracted with `pdfplumber`. The job description and each resume are embedded with `all-MiniLM-L6-v2` and scored by cosine similarity (0 to 100). `generate_feedback` adds a keyword comparison (matched and missing words), a comment on the score and a note if the resume is under 100 or over 500 words. Results are returned sorted by score.

**Other endpoints**

- `/api/register` and `/api/login` use a `Login.users` collection in MongoDB with bcrypt hashes. Passwords need 8+ characters with upper case, lower case, a digit and a special character. A default account `user` / `User@123` is created if the collection is empty.
- `/chat` forwards a question to Gemini with a resume-assistant prompt.

The pages (`login.html`, `home.html`, `Resume_Details.html`, `Resume_ATS.html`) are static HTML that call the Flask server at `localhost:5000` / `127.0.0.1:5000`.

## Stack

Python, Flask, Flask-CORS, google-generativeai, sentence-transformers (PyTorch), pdfplumber, Pillow, pymongo, bcrypt, plain HTML/CSS/JS.

## Setup

You need a MongoDB instance on `mongodb://localhost:27017/` and a Gemini API key. The key is set inline in `App.py` (`os.environ["GEMINI_API_KEY"] = "Your_Gemini_API_Key"`), so replace the placeholder there.

```bash
pip install -r requirements.txt
python App.py
```

Flask runs on port 5000. Then open `login.html` in a browser.

The HTML pages contain hardcoded Windows paths from the original machine (`C:\Deni\Clinics\Resume\venv\Final\...`) for the logo and the Home buttons, so those need editing before the logo and navigation work on another machine.

`sample resumes/` has five PDFs to try the analyzer with.

## Structure

```
App.py                  Flask backend: builder, analyzer, auth, chat
home.html, login.html, Resume_Details.html, Resume_ATS.html, logo.png
templates/              resume templates 1-3 (Jinja) and preview images
sample resumes/         PDFs for testing the analyzer
requirements.txt
```
