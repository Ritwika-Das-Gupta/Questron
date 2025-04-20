# QUESTRON: LLM-Driven AI Interview System,
# Automated Candidate Assessment Using
# Dynamic Question Generation, Speech
# Recognition, and Semantic Response Evaluation

# Questron: LLM-Driven AI Interview System
# Automated Candidate Assessment Using Dynamic Question Generation, Speech Recognition, and Semantic Response Evaluation

"""
Questron is an AI-powered interview system that automates the process of candidate assessment. 
It begins with the user uploading a resume, from which key data (skills, experience, education) is extracted. 
Based on this data, the system generates relevant interview questions and ideal model answers. 
These questions are converted into audio using a Text-to-Speech (TTS) model, and the user listens to them before responding via audio. 
The user's audio responses are transcribed using a Speech-to-Text (STT) model. 
The transcribed answers are then compared with the model's ideal responses using semantic similarity scoring, 
providing a feedback score on how accurately the user's answers align with the expected ones. 
This system automates the entire interview process, providing a seamless and scalable way to assess candidates.
"""






# Explanation of the Code Workflow

This Python script creates an interactive interview system that uses resume data to guide the interview process. The system allows a user to upload a resume (PDF or DOCX), extracts key information (name, skills, experience, and education), and then conducts an interview based on the extracted skills and experience. The responses are processed using a language model (Phi-3.5-mini-instruct), and the similarity between user and model responses is computed. Here's a detailed breakdown of the code:

## Key Libraries Used
- `gradio`: A library for building user interfaces for machine learning models, here used for creating a web interface for the interview process.
- `pdfplumber`, `docx2txt`: Used to extract text from PDF and DOCX files respectively.
- `transformers`: A library for using pretrained models like GPT to generate responses.
- `gTTS`: A text-to-speech library that converts the generated interview questions into audio files.

## Key Functions

### 1. **s2t(filepath)**:
   This function converts audio input into text using the Google speech recognition API. The input is an audio file, and the output is the transcribed text.

### 2. **tts_func(recent_question)**:
   Converts the most recent question into an audio file using the Google Text-to-Speech (gTTS) API. The audio is saved as `output.wav` in the `gradio_temp` directory, which is used to store temporary files.

### 3. **extract_text_from_pdf(pdf_path)**:
   Uses `pdfplumber` to extract all the text from a given PDF file and return it as a string.

### 4. **extract_text_from_docx(docx_path)**:
   Uses `docx2txt` to extract text from a DOCX file and return it as a string.

### 5. **extract_resume_info(text)**:
   This function processes the extracted resume text to find key sections like name, skills, experience, and education. The name is extracted from the first line, and the skills, experience, and education are found using regular expressions to capture sections after the corresponding headers.

### 6. **generate_response(prompt)**:
   Uses the pretrained Phi-3.5-mini-instruct model to generate a response based on the input prompt. The model generates text for follow-up questions, and its output is parsed to extract the generated questions.

### 7. **generate_response_for_df(df)**:
   This function processes the interview questions in a DataFrame by generating responses from the language model. Each question in the DataFrame is passed to the model, and the generated answers are added to a new column `model_response`.

### 8. **calculate_similarity(df)**:
   This function calculates the cosine similarity between the user response and the model's generated response. It uses TF-IDF vectorization to transform the text into numerical representations and then computes similarity between the two responses. The similarity score is then added as a new column in the DataFrame.

### 9. **process_resume(file_path)**:
   Takes in the resume file path, extracts the text using either `extract_text_from_pdf` or `extract_text_from_docx` based on the file type, and returns the extracted text.

### 10. **run_interview(name, skills, experience, user_response=None, questions_and_answers=None, user=None, stage=0)**:
   This is the core function that runs the interview process. Based on the resume data (name, skills, experience), it generates interview questions, processes user responses, and guides the interview through various stages. Each stage involves generating questions based on the user’s previous responses and moving to the next stage of the interview. The function updates the `questions_and_answers` list and the `user` list, which stores the user responses. Once the interview reaches stage 6, it computes the similarity between the user and model responses and saves the results to a CSV file.

### 11. **upload_resume(file_path)**:
   Handles the file upload, processes the resume, and returns extracted details like name, skills, experience, and education.

### 12. **interview_interface(name, skills, experience, user_response=None, stage=0, questions_and_answers=None, user=None)**:
   This function starts and manages the interview interface, invoking `run_interview` and returning the most recent question, the updated stage, and the updated lists of questions and answers.

### 13. **start_interview_from_file(file)**:
   Automatically starts the interview when a resume file is uploaded. It extracts the resume information and calls `interview_interface` to begin the interview process.

## Gradio UI (User Interface)
The user interface is built using Gradio, and it has the following tabs:

### **Tab 1: Upload Resume**
- A user uploads their resume (either PDF or DOCX).
- The `process_resume` function extracts the text, and `extract_resume_info` pulls out the key information.
- The extracted name, skills, experience, and education are displayed in the corresponding output textboxes.

### **Tab 2: Interview**
- This tab allows the user to engage in the interview process.
- After the resume is uploaded, the interview automatically starts, and the first question is generated and spoken to the user via text-to-speech (using the `tts_func`).
- The user responds to the questions, which are recorded via audio input. The `s2t` function converts the response to text.
- Based on the user's responses, the system generates follow-up questions and records them. The model's generated questions and user responses are displayed and stored.

### **Interview Flow**
1. The system asks the user an introductory question related to their experience.
2. The user responds, and a follow-up question is generated based on the user's response.
3. The interview continues in stages, generating questions based on the user’s skills and experience.
4. At the final stage (stage 6), the user responses are compared with the model responses for similarity, and the results are saved to a CSV file.

## Flow Summary
1. The user uploads a resume.
2. The system extracts key details (name, skills, experience, education).
3. The interview starts with a general question related to the user’s experience.
4. The user answers, and follow-up questions are generated based on the responses.
5. The interview continues with skill-based questions.
6. Similarity between user responses and model-generated responses is computed, and results are saved.
7. The entire process is guided by the `run_interview` function, and the state of the interview is tracked using Gradio's `State`.

This approach uses machine learning models (Phi-3.5-mini-instruct) and natural language processing to simulate a conversational interview, allowing the system to adapt the questions based on the user’s resume and responses.
