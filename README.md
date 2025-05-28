# 📝 Resume Builder and Analyzer

A full-stack AI-powered web application to build professional resumes and analyze them against job descriptions for compatibility, relevance, and ATS optimization.

---

## 🚀 Features

### ✅ User Authentication
- Secure registration and login using **bcrypt**.
- Enforces strong password policy.
- Stores user credentials in **MongoDB**.

### 📝 Resume Builder
- Collects comprehensive user information:
  - Personal details, skills, experience, education, projects, certifications, etc.
- AI-generated summaries using **Gemini API**:
  - “About Me”
  - Projects
  - Work experience
  - Internships
- Upload profile image (converted to base64).
- Choose from **3 modern HTML resume templates**:
  - `resume1`, `resume2`, `resume3`

### 📊 Resume Analyzer (ATS Compatibility Checker)
- Upload one or multiple **PDF resumes**.
- Input a **job description**.
- Resume content is parsed and matched against the JD using **semantic similarity** (Sentence-BERT).
- Detailed analysis:
  - Match percentage
  - Keyword optimization
  - Suggestions for improvement
  - Resume length and structure feedback

### 🤖 AI Integration
- Uses **Google Gemini API** for natural language generation.
- Implements **Sentence-BERT (all-MiniLM-L6-v2)** from HuggingFace to compute cosine similarity between resumes and job descriptions.

---

## 🧠 How It Works

### Backend Flow
1. **User Signup/Login**:  
   User info is stored securely with bcrypt-hashed passwords.

2. **Resume Creation**:
   - Resume data is collected via JSON payload.
   - Gemini API generates natural summaries.
   - Resume HTML is rendered using selected template.
   - Resume data saved in MongoDB.

3. **Resume Analysis**:
   - User uploads PDFs and provides a job description.
   - Extracts resume text using `pdfplumber`.
   - Embeds both resume and job description using Sentence-BERT.
   - Calculates similarity score and provides recommendations.

---

## 🧩 Technologies Used

| Technology        | Purpose                                |
|-------------------|----------------------------------------|
| Flask             | Web framework                          |
| MongoDB           | NoSQL database for user/resume storage |
| bcrypt            | Secure password hashing                |
| pdfplumber        | Extracts text from uploaded PDFs       |
| Pillow            | Image manipulation and conversion      |
| Base64            | Encoding uploaded profile images       |
| Google Gemini API | AI-generated summaries                 |
| Sentence-BERT     | Semantic analysis for resume ranking   |
| CORS              | Cross-origin request support           |

---

## 💡 Use Cases

- 🎯 Job Seekers: Build and analyze resumes tailored to job descriptions.
- 🎓 Students: Highlight projects and internships with AI assistance.
- 💼 Career Coaches: Help clients improve resumes with concrete suggestions.
- 🧑‍💼 Recruiters: Batch analyze candidate resumes for role alignment.

---

## 📌 Future Improvements

- [ ] PDF export for generated resumes
- [ ] Real-time job description scraping from job boards
- [ ] Resume editing assistant (chatbot style)
- [ ] User dashboard for managing resumes
- [ ] Multi-language support

---

## ⚙️ How to Run

### 🛠 Prerequisites
- Python 3.8+
- MongoDB (local or cloud instance like MongoDB Atlas)
- Gemini API Key (for AI-generated content)

### 📥 Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/resume-builder-analyzer.git
cd resume-builder-analyzer
```
2. **Install dependencies**
```bash
pip install -r requirements.txt
```
3. **Set environment variables**
   Create a .env file in the root directory and add:
```bash
MONGO_URI=mongodb+srv://<your-connection-string>
GEMINI_API_KEY=your_gemini_api_key_here
```
4. **Run the Flask server**
   Create a .env file in the root directory and add:
```bash
python App.py
```
5. **Open your browser**
```bash
http://localhost:5000
