# StudyVerse
Chatbots for study
import streamlit as st
import pandas as pd
from PyPDF2 import PdfReader
from openai import OpenAI

client = OpenAI(
    api_key="add-api key", //use grok
    base_url="https://api.groq.com/openai/v1"
)

st.set_page_config(
    page_title="StudyVerse AI",
    page_icon="📚",
    layout="wide"
)

st.title("📚 StudyVerse AI")

page = st.sidebar.selectbox(
    "Choose Module",
    [
        "Dashboard",
        "Q&A Assistant",
        "Quiz Generator",
        "PDF Analyzer",
        "Book Recommendations",
        "Study Planner"
    ]
)
# ================= DASHBOARD =================

if page == "Dashboard":

    st.header("🎓 Student Dashboard")

    col1, col2, col3 = st.columns(3)

    with col1:
        st.metric("Subjects", "7")

    with col2:
        st.metric("Books", "12")

    with col3:
        st.metric("Quizzes", "35")

    st.subheader("📚 Study Tips")

    st.info(
        "Study 45 minutes and take a 10 minute break."
    )

    st.subheader("💡 Features")

    st.write("✅ AI Q&A Assistant")
    st.write("✅ Smart Quiz Generator")
    st.write("✅ PDF Notes Analyzer")
    st.write("✅ Study Planner")
    st.write("✅ Book Recommendations")


# ================= Q&A ASSISTANT =================

elif page == "Q&A Assistant":

    st.header("🤖 AI Q&A Assistant")

    question = st.text_input(
        "Ask Any MCA Question"
    )

    if st.button("Get Answer"):

        if question.strip() == "":
            st.warning("Please enter a question.")

        else:

            try:

                response = client.chat.completions.create(
                    model="llama-3.3-70b-versatile",
                    messages=[
                        {
                            "role": "system",
                            "content": """
                            You are an MCA Study Assistant.

                            Answer:
                            - In simple language
                            - Short and clear
                            - Student friendly
                            """
                        },
                        {
                            "role": "user",
                            "content": question
                        }
                    ]
                )

                answer = response.choices[0].message.content

                st.success(answer)

            except Exception as e:

                st.error(f"Error: {e}")
                # ================= QUIZ GENERATOR =================

elif page == "Quiz Generator":

    st.header("🧠 Smart AI Quiz Generator")

    subject = st.selectbox(
        "Select Subject",
        [
            "NLP",
            "AI & ML",
            "Cyber Security",
            "Java",
            "ADBMS",
            "Research Methodology"
        ]
    )

    difficulty = st.selectbox(
        "Difficulty Level",
        [
            "Easy",
            "Medium",
            "Hard"
        ]
    )

    num_questions = st.slider(
        "Number of Questions",
        3,
        10,
        5
    )

    if st.button("Generate Quiz"):

        try:

            response = client.chat.completions.create(
                model="llama-3.3-70b-versatile",

                messages=[
                    {
                        "role": "system",
                        "content": """
                        You are an MCA Quiz Generator.

                        Generate MCQs.

                        Format:

                        Question 1:
                        A)
                        B)
                        C)
                        D)

                        Correct Answer:
                        Explanation:
                        """
                    },

                    {
                        "role": "user",
                        "content": f"""
                        Generate {num_questions}
                        {difficulty} MCQs
                        on {subject}
                        """
                    }
                ]
            )

            quiz = response.choices[0].message.content

            st.subheader("📋 Generated Quiz")

            st.write(quiz)

            st.download_button(
                label="📥 Download Quiz",
                data=quiz,
                file_name=f"{subject}_quiz.txt",
                mime="text/plain"
            )

        except Exception as e:

            st.error(f"Error: {e}")
            # ================= PDF ANALYZER =================

elif page == "PDF Analyzer":

    st.header("📄 Smart PDF Analyzer")

    pdf_file = st.file_uploader(
        "Upload PDF Notes",
        type=["pdf"]
    )

    if pdf_file is not None:

        reader = PdfReader(pdf_file)

        text = ""

        for page_data in reader.pages:

            page_text = page_data.extract_text()

            if page_text:
                text += page_text

        st.subheader("📖 PDF Preview")

        st.text_area(
            "Content",
            text[:3000],
            height=250
        )

        if st.button("Analyze PDF"):

            try:

                response = client.chat.completions.create(
                    model="llama-3.3-70b-versatile",

                    messages=[
                        {
                            "role": "system",
                            "content": """
                            Analyze uploaded notes.

                            Give:

                            1. Main Topic
                            2. Purpose
                            3. Important Points
                            4. Keywords
                            5. Summary
                            6. Conclusion
                            7. Viva Questions
                            """
                        },

                        {
                            "role": "user",
                            "content": text[:5000]
                        }
                    ]
                )

                result = response.choices[0].message.content

                st.subheader("📊 Analysis Result")

                st.write(result)

                st.download_button(
                    label="📥 Download Analysis",
                    data=result,
                    file_name="pdf_analysis.txt",
                    mime="text/plain"
                )

            except Exception as e:

                st.error(f"Error: {e}")
                # ================= BOOK RECOMMENDATIONS =================

elif page == "Book Recommendations":

    st.header("📚 MCA Book Recommendations")

    books = {
        "NLP": [
            "Natural Language Processing with Python",
            "Speech and Language Processing"
        ],

        "AI & ML": [
            "Hands-On Machine Learning",
            "Introduction to Machine Learning"
        ],

        "Cyber Security": [
            "Cyber Security Essentials",
            "Network Security Essentials"
        ],

        "Java": [
            "Head First Java",
            "Effective Java"
        ],

        "ADBMS": [
            "Database System Concepts",
            "Fundamentals of Database Systems"
        ],

        "Research Methodology": [
            "Research Methodology - C.R. Kothari",
            "Practical Research"
        ]
    }

    for subject, booklist in books.items():

        st.subheader(subject)

        for book in booklist:
            st.write("📖", book)

# ================= STUDY PLANNER =================

elif page == "Study Planner":

    st.header("📝 Smart Study Planner")

    subject = st.text_input(
        "Enter Subject"
    )

    exam_date = st.date_input(
        "Exam Date"
    )

    study_hours = st.slider(
        "Study Hours Per Day",
        1,
        12,
        4
    )

    priority = st.selectbox(
        "Priority",
        [
            "High",
            "Medium",
            "Low"
        ]
    )

    if st.button("Generate Study Plan"):

        try:

            response = client.chat.completions.create(
                model="llama-3.3-70b-versatile",

                messages=[
                    {
                        "role": "system",
                        "content": """
                        Create a study timetable.

                        Include:
                        1. Daily Schedule
                        2. Revision Plan
                        3. Important Topics
                        4. Exam Preparation Tips

                        Keep it student friendly.
                        """
                    },

                    {
                        "role": "user",
                        "content": f"""
                        Subject: {subject}

                        Exam Date: {exam_date}

                        Hours Per Day: {study_hours}

                        Priority: {priority}
                        """
                    }
                ]
            )

            plan = response.choices[0].message.content

            st.subheader("📅 Generated Study Plan")

            st.write(plan)

            st.download_button(
                label="📥 Download Study Plan",
                data=plan,
                file_name="study_plan.txt",
                mime="text/plain"
            )

        except Exception as e:

            st.error(f"Error: {e}")

# ================= FOOTER =================

st.markdown("---")

st.markdown(
    """
    ### 🎓 StudyVerse AI

    MCA Project

    Features:
    - AI Q&A Assistant
    - AI Quiz Generator
    - Smart PDF Analyzer
    - Book Recommendation System
    - Study Planner

    Developed using Python, Streamlit and Grok AI.
    """
)
