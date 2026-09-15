# Face Recognition Attendance System

A Python-based **Face Recognition Attendance System** that automates student attendance using a webcam. The system detects faces using **Haar Cascade**, recognizes registered students using the **LBPH (Local Binary Patterns Histograms)** algorithm, and stores student and attendance information in **Supabase**.

## 📌 Project Overview

Traditional attendance systems can require manual effort and may be time-consuming. This project provides a simple automated solution in which a student's face is detected and recognized through a webcam.

The system consists of three main stages:

1. **Student Registration** - Collect student information and capture face images.
2. **Model Training** - Train an LBPH face recognizer using the collected images.
3. **Attendance** - Recognize the student's face and record attendance in Supabase.

## ✨ Features

* Student registration with:

  * Student ID
  * Name
  * Branch
  * Email
* Webcam-based face detection
* Automatic capture of **30 face images** during registration
* Face recognition using LBPH
* Haar Cascade-based face detection
* Trained model saved as `trainer.yml`
* Cloud-based student and attendance data storage using Supabase
* Attendance date and time recording
* Duplicate attendance prevention for the same student on the same date
* Real-time display of the recognized student's name and ID

## 🛠️ Technologies Used

| Technology   | Purpose                                                 |
| ------------ | ------------------------------------------------------- |
| Python       | Main programming language                               |
| OpenCV       | Computer vision, webcam, face detection and recognition |
| Haar Cascade | Face detection                                          |
| LBPH         | Face recognition                                        |
| NumPy        | Image and ID array processing                           |
| PIL          | Image loading and grayscale conversion                  |
| Supabase     | Cloud database                                          |
| Webcam       | Real-time image/video input                             |

## 📂 Project Structure

```text
Face-Attendance/
│
├── dataset/
│   └── User.*.jpg
│
├── trainer/
│   └── trainer.yml
│
├── register.py
├── train.py
├── attendance.py
└── README.md
```

### `register.py`

Registers a student and captures their facial images.

The program:

* Takes Student ID, Name, Branch, and Email.
* Stores student information in the `students` table.
* Opens the webcam.
* Detects faces using Haar Cascade.
* Converts detected faces to grayscale.
* Saves up to 30 face images in the `dataset` directory.

Example filename:

```text
User.1.1.jpg
User.1.2.jpg
User.1.3.jpg
```

The number `1` represents the student's ID.

### `train.py`

Trains the LBPH face recognizer.

The program:

1. Reads images from the `dataset` directory.
2. Converts images to grayscale.
3. Extracts the student ID from each filename.
4. Trains the LBPH recognizer.
5. Saves the trained model as:

```text
trainer/trainer.yml
```

### `attendance.py`

Runs the real-time attendance system.

The program:

1. Loads the trained `trainer.yml`.
2. Opens the webcam.
3. Detects faces using Haar Cascade.
4. Uses LBPH to recognize the detected face.
5. Obtains the student's ID.
6. Retrieves the student's information from Supabase.
7. Checks whether attendance has already been recorded for the current date.
8. Inserts attendance date and time if no previous record exists.

## 🔄 System Workflow

```text
                 STUDENT REGISTRATION
                         │
                         ▼
                Enter Student Details
                         │
                         ▼
                   Open Webcam
                         │
                         ▼
                  Detect Face
                         │
                         ▼
                Capture 30 Images
                         │
                         ▼
                    Dataset
                         │
                         ▼
                   TRAIN MODEL
                         │
                         ▼
                 LBPH Face Model
                         │
                         ▼
                  trainer.yml
                         │
                         ▼
                ATTENDANCE SYSTEM
                         │
                         ▼
                    Open Webcam
                         │
                         ▼
                   Detect Face
                         │
                         ▼
                  LBPH Prediction
                         │
                         ▼
                   Student ID
                         │
                         ▼
                 Check Supabase
                         │
                  ┌──────┴──────┐
                  │             │
             Already Exists   Not Exists
                  │             │
                  ▼             ▼
             No Duplicate   Mark Attendance
                                │
                                ▼
                         Date + Time Saved
```

## 🧠 Face Recognition Approach

### Haar Cascade

Haar Cascade is used to **detect** faces in webcam frames.

```python
face_detector = cv2.CascadeClassifier(
    cv2.data.haarcascades +
    'haarcascade_frontalface_default.xml'
)
```

### LBPH

LBPH is used to **recognize** the detected face.

```python
recognizer = cv2.face.LBPHFaceRecognizer_create()
```

The important distinction is:

```text
Haar Cascade → Where is the face?
LBPH         → Whose face is it?
```

## 🗄️ Database

The project uses Supabase for storing student and attendance information.

### Students Table

The registration program stores:

```text
student_id
name
branch
email
```

### Attendance Table

The attendance program stores:

```text
student_id
attendance_date
attendance_time
```

## 🚫 Duplicate Attendance

Before inserting a new attendance record, the system checks Supabase using:

```text
Student ID + Attendance Date
```

If an attendance record already exists for that student on the current date, another record is not inserted.

This prevents multiple attendance records for the same student on the same day.

## ⚙️ Installation

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd Face-Attendance
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it on Windows:

```bash
venv\Scripts\activate
```

### 3. Install dependencies

Install the required Python libraries:

```bash
pip install opencv-python
pip install opencv-contrib-python
pip install numpy
pip install pillow
pip install supabase
```

> `opencv-contrib-python` is required because the project uses `cv2.face.LBPHFaceRecognizer_create()`.

## 🔧 Supabase Configuration

Create a Supabase project and configure the required database tables.

The application requires:

```text
SUPABASE_URL
SUPABASE_KEY
```

### Security Recommendation

Do **not** commit database credentials directly to GitHub.

Instead, store credentials in environment variables or a `.env` file.

Example:

```env
SUPABASE_URL=your_supabase_url
SUPABASE_KEY=your_supabase_key
```

Then access them in Python using environment variables.

If credentials have already been exposed in a public repository, rotate/revoke the affected key before publishing the project.

## ▶️ How to Run

### Step 1: Register a Student

Run:

```bash
python register.py
```

Enter:

```text
Student ID
Student Name
Branch
Email
```

The webcam will open and capture the student's face images.

Press:

```text
ESC
```

to stop capturing early.

Otherwise, the system stops after collecting 30 images.

### Step 2: Train the Model

Run:

```bash
python train.py
```

After successful training:

```text
Training Complete
```

The trained model will be saved as:

```text
trainer/trainer.yml
```

### Step 3: Start Attendance

Run:

```bash
python attendance.py
```

The webcam will detect and recognize registered students.

When a valid face is recognized, the system checks the database and records attendance if the student has not already been marked for that date.

## 🎯 Recognition Threshold

The current implementation uses:

```python
if confidence < 60:
```

as the recognition condition.

The threshold should ideally be evaluated and tuned using a representative validation dataset rather than being treated as a universal accuracy value.

## ⚠️ Current Limitations

The current implementation has several limitations:

* LBPH is less robust than modern deep-learning-based face recognition approaches.
* Recognition can be affected by poor lighting.
* Large changes in face angle may reduce recognition performance.
* The current system does not implement dedicated **liveness detection**.
* The project is primarily suitable for small-scale deployment.
* A formal accuracy benchmark is not included in the current implementation.
* Database credentials should be moved out of the source code before production deployment.

## 🚀 Future Improvements

Possible improvements include:

* Add **liveness detection** to prevent photograph-based spoofing.
* Replace LBPH with a modern deep-learning face recognition model.
* Improve recognition under different lighting conditions.
* Support multiple cameras.
* Add an admin dashboard.
* Add attendance analytics and reports.
* Export attendance to Excel or PDF.
* Add authentication for administrators.
* Improve database security.
* Deploy the application as a web-based system.
* Add notifications for attendance records.

## 🔒 Security Considerations

Because this project processes biometric information, a production implementation should consider:

* Secure storage of face-related data.
* Proper authentication and authorization.
* Encryption where appropriate.
* Secure API credential management.
* Access control for attendance records.
* Data retention and deletion policies.
* Protection against spoofing and unauthorized access.

## 📚 Learning Outcomes

This project provides practical experience with:

* Python programming
* Computer vision
* Image preprocessing
* Face detection
* Face recognition
* LBPH
* OpenCV
* Webcam processing
* NumPy
* Database integration
* Supabase
* Real-time application development

## 📄 License

This project can be used for educational and learning purposes. Add an appropriate open-source license if you plan to distribute the project publicly.

---

## 👨‍💻 Project Summary

**Face Recognition Attendance System** combines computer vision and cloud database technology to automate student attendance.

```text
Python + OpenCV
       │
       ├── Haar Cascade → Face Detection
       │
       ├── LBPH → Face Recognition
       │
       └── Supabase → Student & Attendance Data
```

The project demonstrates how computer vision can be integrated with a database to create a practical real-world automation system.
