# AI-CHAT-BOT-HEALTH-
This project aims to create an AI-powered chatbox designed to provide mental health support, offering users a safe and empathetic space for conversation. The chatbox utilizes natural language processing (NLP) to understand user input and provide appropriate, calming, and supportive responses. 
// APP.PY
from flask import Flask, render_template, request, jsonify
import logging
import requests
import os
import google.generativeai as genai


logging.basicConfig(level=logging.DEBUG)  

app = Flask(__name__)


GEMINI_API_KEY = os.getenv("GEMINI_API_KEY") 


if not GEMINI_API_KEY:
    
    GEMINI_API_KEY="...."




@app.route("/")
def home():
    return render_template("index.html")

@app.route("/chat", methods=["POST"])
def chat():
    user_input = request.json.get("message", "").strip()
    if not user_input:
        return jsonify({"reply": "I'm here to listen. Please share your thoughts."}), 200

    try:
        
        response = requests.post(
            "https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=A.....",  # Replace with actual Gemini API endpoint
            headers={
                "Content-Type": "application/json"
            },
            json={
                "contents": [{
                    "parts":[{"text": "You are a chat bot which deals with mental health issues. Provide helpful and supportive replies.\n\nUser Input: " + user_input}]
                    }]
            }
        )
      

    
        logging.debug(f"Gemini API response: {response.text}")

        
        response_data = response.json()
        
        
        logging.debug(f"Parsed response: {response_data}")

        
        choices = response_data.get("candidates", [])
        print(choices)
        if choices:
            reply = choices[0].get("content", {}).get("parts",[])[0].get("text").strip()
        else:
            reply = "I'm here for you. Can you tell me more about how you're feeling?"

        if not reply:
            reply = "I'm here for you. Can you tell me more about how you're feeling?"
            
        return jsonify({"reply": reply})

    except requests.exceptions.RequestException as e:
        logging.error(f"Request error: {e}")
        return jsonify({"reply": "I'm sorry, I encountered an error while processing your request. Please try again later."}), 500
    except Exception as e:
        logging.error(f"General error: {e}")
        return jsonify({"reply": "I'm sorry, I encountered an error while processing your request. Please try again later."}), 500

if __name__ == "__main__":
    app.run(debug=True)



// HTML ,CSS
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Mental Health Chatbot</title>
  
</head>
<body>
    <div class="container">
        <header>
            <h1>MindSphere</h1>
            <p>Your AI-powered mental health companion</p>
        </header>
        <div class="chat-container">
            <div id="chatbox" class="chatbox">
                <div class="bot-message">
                    <span class="avatar">🤖</span> 
                    <p>Hello! I’m here to support you. How are you feeling today?</p>
                </div>
            </div>
            <form id="chat-form" class="chat-form">
                <input 
                    type="text" 
                    id="user-input" 
                    placeholder="Type your message..." 
                    autocomplete="off" />
                <button type="submit">➤</button>
            </form>
        </div>
    </div>
    <style>
   
    body {
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        margin: 0;
        padding: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        min-height: 100vh;
        background: linear-gradient(135deg, #2a2a72, #009ffd);
        color: white;
        overflow: hidden;
    }
    
   
    .container {
        width: 90%;
        max-width: 450px;
        background: rgba(255, 255, 255, 0.1);
        padding: 20px;
        border-radius: 20px;
        box-shadow: 0px 10px 30px rgba(0, 0, 0, 0.5);
        backdrop-filter: blur(15px);
        transition: all 0.3s ease-in-out;
    }
    
    .container:hover {
        transform: translateY(-5px);
        box-shadow: 0px 15px 40px rgba(0, 0, 0, 0.6);
    }
    
    
    header {
        text-align: center;
        margin-bottom: 20px;
    }
    
    header h1 {
        font-size: 2.5em;
        color: #00c6ff;
        text-shadow: 0 0 10px #00c6ff, 0 0 20px #00c6ff;
    }
    
    header p {
        font-size: 1em;
        color: #b0e0ff;
    }
    
   
    .chat-container {
        position: relative;
        background: #1b1b2f;
        border-radius: 15px;
        height: 500px;
        display: flex;
        flex-direction: column;
        overflow: hidden;
        box-shadow: inset 0px 0px 15px rgba(0, 0, 0, 0.6);
    }
    
    .chatbox {
        flex: 1;
        padding: 15px;
        overflow-y: auto;
        scrollbar-width: thin;
        scrollbar-color: #00c6ff transparent;
        animation: fadeIn 0.5s ease-in-out;
    }
    
    .chatbox::-webkit-scrollbar {
        width: 8px;
    }
    
    .chatbox::-webkit-scrollbar-thumb {
        background: linear-gradient(45deg, #00c6ff, #009ffd);
        border-radius: 10px;
    }
    
    .bot-message,
    .user-message {
        display: flex;
        align-items: center;
        margin-bottom: 15px;
        animation: slideIn 0.4s ease-in-out;
    }
    
    .bot-message {
        justify-content: flex-start;
        color: #00c6ff;
    }
    
    .user-message {
        justify-content: flex-end;
        color: #32cd32;
    }
    
    .bot-message p,
    .user-message p {
        max-width: 70%;
        background: rgba(255, 255, 255, 0.2);
        padding: 10px;
        border-radius: 15px;
        box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
        text-align: left;
        transition: transform 0.3s ease-in-out;
    }
    
    .bot-message p:hover,
    .user-message p:hover {
        transform: scale(1.05);
    }
    
    .user-message p {
        background: linear-gradient(135deg, #32cd32, #8fbc8f);
        color: #000;
    }
    
   
    .avatar {
        margin-right: 10px;
        font-size: 1.5em;
    }
    
   
    .chat-form {
        display: flex;
        align-items: center;
        padding: 10px;
        background: rgba(0, 0, 0, 0.8);
        border-top: 1px solid rgba(255, 255, 255, 0.2);
        border-radius: 0 0 15px 15px;
    }
    
    #user-input {
        flex: 1;
        padding: 15px;
        background: #2c2c2c;
        border: none;
        border-radius: 20px;
        color: white;
        font-size: 1em;
        outline: none;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
        transition: background 0.3s ease-in-out;
    }
    
    #user-input:focus {
        background: #3a3a3a;
    }
    
    #user-input::placeholder {
        color: #888;
    }
    
    button {
        margin-left: 10px;
        padding: 10px 15px;
        background: linear-gradient(135deg, #00c6ff, #009ffd);
        color: white;
        border: none;
        border-radius: 50%;
        font-size: 1.5em;
        cursor: pointer;
        transition: transform 0.3s ease, box-shadow 0.3s ease;
        box-shadow: 0 4px 15px rgba(0, 198, 255, 0.6);
    }
    
    button:hover {
        transform: scale(1.1);
        box-shadow: 0 6px 20px rgba(0, 198, 255, 0.8);
    }
    
    button:active {
        transform: scale(0.9);
        box-shadow: 0 4px 15px rgba(0, 198, 255, 0.4);
    }
    
  
    @keyframes fadeIn {
        from {
            opacity: 0;
            transform: translateY(10px);
        }
        to {
            opacity: 1;
            transform: translateY(0);
        }
    }
    
    @keyframes slideIn {
        from {
            transform: translateX(20px);
            opacity: 0;
        }
        to {
            transform: translateX(0);
            opacity: 1;
        }
    }
   </style>
    <script src="{{ url_for('static', filename='script.js') }}"></script>
</body>
</html>




// JS
// Handle form submission
document.getElementById("chat-form").addEventListener("submit", async (e) => {
    e.preventDefault();

    // Get user input
    const userInput = document.getElementById("user-input").value.trim();
    if (!userInput) return;

    // Display the user's message
    displayMessage(userInput, "user-message");

    // Clear input field
    document.getElementById("user-input").value = "";

    // Add a "typing" indicator
    const chatbox = document.getElementById("chatbox");
    const typingIndicator = document.createElement("div");
    typingIndicator.className = "bot-message typing-indicator";
    typingIndicator.innerHTML = `<span class="avatar">🤖</span> <p>Typing...</p>`;
    chatbox.appendChild(typingIndicator);
    chatbox.scrollTop = chatbox.scrollHeight;

    // Fetch bot response
    try {
        const response = await fetch("/chat", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify({ message: userInput }),
        });

        const data = await response.json();

        // Remove typing indicator
        chatbox.removeChild(typingIndicator);

        // Display the bot's message
        displayMessage(data.reply, "bot-message");
    } catch (error) {
        console.error("Error:", error);

        // Remove typing indicator and display error
        chatbox.removeChild(typingIndicator);
        displayMessage("I'm sorry, I encountered an error. Please try again later.", "bot-message");
    }
});

// Function to display messages in the chatbox
function displayMessage(message, className) {
    const chatbox = document.getElementById("chatbox");

    const messageContainer = document.createElement("div");
    messageContainer.className = className;

    const avatar = document.createElement("span");
    avatar.className = "avatar";

    if (className === "bot-message") {
        avatar.textContent = "🤖";
        messageContainer.appendChild(avatar);
    }

    const messageBubble = document.createElement("p");
    messageBubble.textContent = message;

    messageContainer.appendChild(messageBubble);
    chatbox.appendChild(messageContainer);

    // Scroll to the latest message
    chatbox.scrollTop = chatbox.scrollHeight;
}





