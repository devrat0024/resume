# resume
import spacy

def parse_resume(text):
    nlp = spacy.load("en_core_web_sm")
    doc = nlp(text)

    # Extracting information
    name = None
    email = None
    phone = None
    skills = []
    education = []
    experience = []

    for ent in doc.ents:
        if ent.label_ == "PERSON" and not name:
            name = ent.text
        elif ent.label_ == "EMAIL" and not email:
            email = ent.text
        elif ent.label_ == "PHONE_NUMBER" and not phone:
            phone = ent.text

    for token in doc:
        if token.text.lower() == "skills":
            skills = [t.text for t in token.head.children if t.dep_ == "conj" and t.text not in ["and", ","]]

        if "education" in token.text.lower():
            education = [ent.text for ent in doc.ents if ent.root.head.text == token.text]

        if "experience" in token.text.lower():
            experience = [ent.text for ent in doc.ents if ent.root.head.text == token.text]

    return {
        "Name": name,
        "Email": email,
        "Phone": phone,
        "Skills": skills,
        "Education": education,
        "Experience": experience
    }

# Example usage
resume_text = """
John Doe
Email: john.doe@email.com
Phone: (555) 123-4567

Skills: Python, Java, Data Analysis

Education:
Bachelor of Science in Computer Science - XYZ University - 2020

Experience:
Software Developer - ABC Company - 2020-present
"""

parsed_resume = parse_resume(resume_text)
print(parsed_resume)
