# ml-service 🤖

Mentora's Machine Learning microservice — a Flask REST API that serves the custom-trained scikit-learn model used for career role prediction and skill gap analysis.

This service is developed and deployed independently from the main Next.js application as part of the **Mentora – AI Career Coach** platform.

---

## 📌 What It Does

The ML service provides machine-learning-based career insights for Mentora users.

It primarily performs:

- **Career role prediction** based on a user's current skills
- **Skill gap analysis** for a selected target role
- Identification of **matched and missing skills**
- **Prioritized learning recommendations**
- Role suggestions based on the user's current skill set

The service is separated from the main Next.js application so that the Python machine-learning ecosystem can remain isolated from the Node.js frontend.

This architecture allows the ML model and its dependencies to be developed, updated, and deployed independently.

---

## 🛠️ Tech Stack

| Library | Version | Purpose |
|---|---:|---|
| Flask | 3.0.0 | REST API framework |
| Flask-CORS | 4.0.0 | Cross-origin requests from the Next.js application |
| scikit-learn | 1.4.0 | Machine learning model and TF-IDF processing |
| pandas | 2.1.4 | Data processing |
| numpy | 1.26.3 | Numerical operations |
| joblib | 1.3.2 | Model and vectorizer serialization |
| gunicorn | 21.2.0 | Production WSGI server |

---

## 📁 Project Structure

```text
ml-service/
├── app.py                  # Flask application and API routes
├── train_model.py          # Model training script
├── utils.py                # Helper and preprocessing functions
├── model/
│   ├── skill_model.pkl     # Trained Logistic Regression model
│   ├── vectorizer.pkl      # TF-IDF vectorizer
│   └── skills_dict.json    # Required skills mapped to supported roles
├── data/                   # Training data
├── requirements.txt        # Python dependencies
├── runtime.txt             # Python runtime configuration
└── Procfile                # Production deployment configuration

🚀 Getting Started
Prerequisites
Python 3.10+
pip
Git
Clone the Repository
git clone https://github.com/ishannamata/ml-service.git
cd ml-service
Create a Virtual Environment
Windows
python -m venv venv
venv\Scripts\activate
macOS / Linux
python3 -m venv venv
source venv/bin/activate
Install Dependencies
pip install -r requirements.txt
Train the Model

If the trained model artifacts need to be regenerated:

python train_model.py

The trained model and supporting artifacts are stored inside the model/ directory.

Run the Development Server
python app.py

The service runs locally on:

http://localhost:5001
📡 API Reference
1. Health Check
GET /health

Checks whether the ML service is running.

Response
{
  "status": "ok",
  "message": "Skill Gap ML service running"
}
2. Skill Gap Prediction
POST /predict

Performs career role prediction and skill gap analysis.

Request Body
{
  "user_skills": [
    "Python",
    "React",
    "SQL"
  ],
  "target_role": "Full Stack Developer"
}
Parameters
Parameter	Type	Required	Description
user_skills	Array	Yes	Skills currently possessed by the user
target_role	String	No	Target career role. If omitted, the ML model predicts suitable roles
Response
{
  "target_role": "Full Stack Developer",
  "match_score": 50.0,
  "matched_skills": [
    "Python",
    "React",
    "SQL"
  ],
  "missing_skills": [
    "JavaScript",
    "MongoDB"
  ],
  "total_required": 5,
  "role_suggestions": [],
  "recommendations": [
    {
      "skill": "Javascript",
      "priority": "High",
      "learning_time": "2-4 weeks"
    }
  ]
}

The exact response depends on the user's submitted skills and selected target role.

3. Get Supported Roles
GET /roles

Returns all career roles supported by the ML service.

Response
{
  "roles": [
    "Full Stack Developer",
    "Software Engineer",
    "Backend Developer"
  ]
}
4. Get Skills for a Role
GET /skills/<role>

Returns the required skills for a specific supported role.

Example
GET /skills/Full%20Stack%20Developer
Response
{
  "role": "Full Stack Developer",
  "skills": [
    "Python",
    "React",
    "SQL"
  ]
}
🧠 Machine Learning Model

The Skill Gap Analyzer uses a combination of:

TF-IDF Vectorization

TF-IDF (Term Frequency–Inverse Document Frequency) converts skill-related text into numerical feature vectors that can be processed by the machine learning model.

Logistic Regression

A Logistic Regression classifier is used for career-role prediction based on the user's skill set.

Skill Gap Analysis

For a selected target role, the service compares the user's current skills against the required skills stored in:

model/skills_dict.json

The service then calculates:

Match percentage
Matched skills
Missing skills
Priority of missing skills
Estimated learning time
Alternative role suggestions
🔄 Prediction Flow
User Skills
     │
     ▼
Skill Normalization
     │
     ▼
TF-IDF Vectorization
     │
     ▼
Logistic Regression Model
     │
     ├───────────────► Role Suggestions
     │
     ▼
Target Role
     │
     ▼
Required Skills Lookup
     │
     ▼
Compare User Skills
     │
     ├───────────────► Matched Skills
     │
     └───────────────► Missing Skills
                              │
                              ▼
                    Priority Recommendations
🌍 Deployment

The ML service is deployed independently on Render.

Production URL
https://ml-service-b5jo.onrender.com
Health Check
https://ml-service-b5jo.onrender.com/health

A successful health check returns:

{
  "status": "ok",
  "message": "Skill Gap ML service running"
}
Render Configuration

Build Command

pip install -r requirements.txt

Start Command

gunicorn app:app

Python Version

3.11.11

Health Check Path

/health
🔗 Integration with Mentora

The main Mentora Next.js application communicates with this ML service through the following environment variable:

ML_SERVICE_URL=https://ml-service-b5jo.onrender.com

The Next.js application uses this URL to communicate with the ML API.

For example:

Mentora Next.js Application
          │
          │ POST /predict
          ▼
Render ML Service
          │
          ▼
TF-IDF + Logistic Regression
          │
          ▼
Skill Gap Analysis
          │
          ▼
Mentora UI
⚙️ Environment Variables

The ML service itself does not require an API key for the current prediction endpoints.

The main Mentora application uses:

ML_SERVICE_URL=https://ml-service-b5jo.onrender.com


📝 Notes
The trained model artifacts are stored inside the model/ directory.
joblib is used to serialize the trained model and TF-IDF vectorizer.
The service uses Flask-CORS so that the deployed Next.js application can communicate with the API.
The production server uses Gunicorn.
The Render free instance may spin down after inactivity, so the first request after a period of inactivity may take longer.
The / route is not defined; use /health to verify that the service is running.
👥 Authors

Built with ❤️‍🔥 by Ishan and Ishani as part of the Mentora – AI Career Coach project.
