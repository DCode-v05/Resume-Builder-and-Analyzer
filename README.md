# Resume Builder and Analyzer

## Project Description
Resume Builder and Analyzer is a full-stack AI-powered web application designed to help users create professional resumes and analyze them for compatibility with job descriptions. The platform leverages advanced AI models to generate content, assess resume quality, and provide actionable feedback for job seekers, students, and career professionals.

---

## Project Details

### User Authentication
- Secure registration and login using bcrypt.
- Enforces strong password policy.
- User credentials are stored securely in MongoDB.

### Resume Builder
- Collects comprehensive user information: personal details, skills, experience, education, projects, certifications, and more.
- AI-generated summaries for sections like "About Me," projects, and work experience using the Gemini API.
- Profile image upload and conversion to base64.
- Offers three modern HTML resume templates for customization.

### Resume Analyzer (ATS Compatibility Checker)
- Upload one or multiple PDF resumes.
- Input a job description for analysis.
- Parses and matches resume content against the job description using semantic similarity (Sentence-BERT).
- Provides detailed analysis: match percentage, keyword optimization, improvement suggestions, and feedback on resume structure.

### AI Integration
- Utilizes Google Gemini API for natural language generation.
- Implements Sentence-BERT (all-MiniLM-L6-v2) for semantic analysis and ranking.

---

## Tech Stack
- **Backend:** Flask (Python)
- **Database:** MongoDB
- **Authentication:** bcrypt
- **AI/NLP:** Google Gemini API, Sentence-BERT (HuggingFace)
- **PDF Processing:** pdfplumber
- **Image Processing:** Pillow, Base64
- **Frontend:** HTML templates
- **Other:** CORS

---

## Getting Started

### Prerequisites
- Python 3.8 or higher
- MongoDB (local or cloud instance, e.g., MongoDB Atlas)
- Gemini API Key (for AI-generated content)

### Installation
1. **Clone the repository:**
   ```bash
   git clone https://github.com/DCode-v05/Resume-Builder-and-Analyzer.git
   cd Resume-Builder-and-Analyzer
   ```
2. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```
3. **Set environment variables:**
   Create a `.env` file in the root directory and add:
   ```bash
   MONGO_URI=mongodb+srv://<your-connection-string>
   GEMINI_API_KEY=your_gemini_api_key_here
   ```
4. **Run the Flask server:**
   ```bash
   python App.py
   ```
5. **Open your browser:**
   Visit `http://localhost:5000`

---

## Usage
- Register or log in to your account.
- Build your resume by entering your details and selecting a template.
- Download or analyze your resume by uploading it along with a job description.
- Review the analysis and suggestions to improve your resume's compatibility with job descriptions.

---

## Project Structure
```
Resume-Builder-and-Analyzer/
│
├── App.py                  # Main Flask application
├── requirements.txt        # Python dependencies
├── README.md               # Project documentation
├── logo.png                # Project logo
├── home.html               # Home page template
├── login.html              # Login page template
├── Resume_ATS.html         # Resume analysis page
├── Resume_Details.html     # Resume details page
│
├── sample resumes/         # Sample PDF resumes for testing
│   ├── C1061.pdf
│   ├── C1070.pdf
│   ├── C1080.pdf
│   ├── C1161.pdf
│   └── C1164.pdf
│
├── templates/              # Resume templates and images
│   ├── Resume_Template1.html
│   ├── Resume_Template2.html
│   ├── Resume_Template3.html
│   ├── Resume1.png
│   ├── Resume2.png
│   └── Resume3.png
```

---

## Contributing

Contributions are welcome! To contribute:
1. Fork the repository
2. Create a new branch:
   ```bash
   git checkout -b feature/your-feature
   ```
3. Commit your changes:
   ```bash
   git commit -m "Add your feature"
   ```
4. Push to your branch:
   ```bash
   git push origin feature/your-feature
   ```
5. Open a pull request describing your changes.

---

## Contact
- **GitHub:** [https://github.com/DCode-v05](https://github.com/DCode-v05)
- **Email:** denistanb05@gmail.com
