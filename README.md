# shardul
from flask import Flask, render_template, request, jsonify
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

app = Flask(__name__)

# Language Options
LANGUAGES = {
    "English": {"submit": "Submit", "welcome": "Welcome to the SRH Guide Bot"},
    "German": {"submit": "Absenden", "welcome": "Willkommen beim SRH Guide Bot"}
}

@app.route('/')
def home():
    return render_template('index.html')

@app.route('/send_query', methods=['POST'])
def send_query():
    data = request.json
    name = data.get("name")
    email = data.get("email")
    message = data.get("message")
    
    if not name or not email or not message:
        return jsonify({"status": "error", "message": "All fields are required!"}), 400

    # Send Email
    try:
        send_email(name, email, message)
        return jsonify({"status": "success", "message": "Query Sent Successfully!"})
    except Exception as e:
        return jsonify({"status": "error", "message": str(e)}), 500

def send_email(name, email, message):
    sender_email = "your-email@example.com"
    receiver_email = "university@example.com"
    smtp_server = "smtp.example.com"
    smtp_port = 587
    smtp_username = "your-email@example.com"
    smtp_password = "your-password"

    msg = MIMEMultipart()
    msg["From"] = sender_email
    msg["To"] = receiver_email
    msg["Subject"] = f"New Query from {name}"

    body = f"Name: {name}\nEmail: {email}\nMessage: {message}"
    msg.attach(MIMEText(body, "plain"))

    server = smtplib.SMTP(smtp_server, smtp_port)
    server.starttls()
    server.login(smtp_username, smtp_password)
    server.sendmail(sender_email, receiver_email, msg.as_string())
    server.quit()

if __name__ == '__main__':
    app.run(debug=True)
