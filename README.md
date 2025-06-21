# Real-time-chat-application
#Websocket
// server.js
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8080 });

let clients = [];

wss.on('connection', (ws) => {
  clients.push(ws);
  console.log("Client connected");

  ws.on('message', (message) => {
    console.log('Received:', message);
    clients.forEach(client => {
      if (client.readyState === WebSocket.OPEN) {
        client.send(message);
      }
    });
  });

  ws.on('close', () => {
    clients = clients.filter(client => client !== ws);
    console.log("Client disconnected");
  });
});

console.log("WebSocket server running on ws://localhost:8080");

#HTML file

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Real-Time Chat App</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f1f1f1;
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }

    .chat-container {
      background: #fff;
      width: 90%;
      max-width: 500px;
      border-radius: 10px;
      box-shadow: 0 10px 25px rgba(0,0,0,0.1);
      padding: 20px;
      display: flex;
      flex-direction: column;
    }

    .chat-header {
      font-size: 20px;
      font-weight: bold;
      margin-bottom: 10px;
      text-align: center;
    }

    .chat-box {
      flex: 1;
      border: 1px solid #ccc;
      padding: 10px;
      overflow-y: auto;
      height: 300px;
      border-radius: 5px;
      background-color: #fafafa;
      margin-bottom: 10px;
    }

    .message {
      margin: 5px 0;
      padding: 8px 10px;
      border-radius: 5px;
      max-width: 70%;
      word-wrap: break-word;
    }

    .message.me {
      background-color: #dcf8c6;
      align-self: flex-end;
    }

    .message.other {
      background-color: #f1f0f0;
      align-self: flex-start;
    }

    .input-area {
      display: flex;
      gap: 10px;
    }

    input {
      flex: 1;
      padding: 10px;
      font-size: 14px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }

    button {
      padding: 10px 15px;
      font-size: 14px;
      background-color: #007bff;
      border: none;
      color: white;
      border-radius: 5px;
      cursor: pointer;
    }

    button:hover {
      background-color: #0056b3;
    }
  </style>
</head>
<body>
  <div class="chat-container">
    <div class="chat-header">💬 Real-Time Chat</div>
    <div class="chat-box" id="chat-box"></div>
    <div class="input-area">
      <input type="text" id="msgInput" placeholder="Type a message..." />
      <button onclick="sendMessage()">Send</button>
    </div>
  </div>

  <script>
    const ws = new WebSocket('ws://localhost:8080');
    const chatBox = document.getElementById('chat-box');
    const input = document.getElementById('msgInput');

    function appendMessage(text, from) {
      const msgDiv = document.createElement('div');
      msgDiv.classList.add('message', from);
      msgDiv.innerText = text;
      chatBox.appendChild(msgDiv);
      chatBox.scrollTop = chatBox.scrollHeight;
    }

    ws.onmessage = (event) => {
      appendMessage(event.data, 'other');
    };

    function sendMessage() {
      const message = input.value.trim();
      if (message !== '') {
        ws.send(message);
        appendMessage(message, 'me');
        input.value = '';
      }
    }

    input.addEventListener('keydown', function (e) {
      if (e.key === 'Enter') sendMessage();
    });
  </script>
</body>
</html>
