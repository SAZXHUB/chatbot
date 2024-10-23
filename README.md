
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gemini AI Chat</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.21.1/axios.min.js"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        /* สไตล์เดิม */
        body { margin: 0; font-family: Arial, sans-serif; display: flex; }
        .sidebar { width: 260px; background-color: #202123; padding: 10px; height: 100vh; color: white; }
        .main-content { flex-grow: 1; display: flex; flex-direction: column; height: 100vh; background-color: #343541; }
        .new-chat-button { width: 100%; padding: 10px; background-color: #2d2d2d; border: 1px solid #565869; border-radius: 5px; color: white; cursor: pointer; display: flex; align-items: center; gap: 10px; }
        .new-chat-button:hover { background-color: #404040; }
        .chat-container { flex-grow: 1; overflow-y: auto; padding: 20px; }
        .input-container { padding: 20px; background-color: #343541; }
        .input-group { max-width: 800px; margin: 0 auto; }
        .input-wrapper { display: flex; gap: 10px; background-color: #40414f; padding: 10px; border-radius: 5px; }
        textarea { flex-grow: 1; background: transparent; border: none; color: white; resize: none; font-size: 16px; }
        textarea:focus { outline: none; }
        .send-button { background: transparent; border: none; color: #565869; cursor: pointer; padding: 5px 10px; }
        .send-button:hover { color: white; }
        .message { display: flex; gap: 20px; margin-bottom: 20px; padding: 20px; }
        .message:nth-child(odd) { background-color: #444654; }
        .avatar { width: 30px; height: 30px; border-radius: 3px; display: flex; align-items: center; justify-content: center; }
        .avatar.gemini { background-color: #19c37d; color: white; }
        .avatar.user { background-color: #8e8ea0; color: white; }
        .message-content { flex-grow: 1; color: white; line-height: 1.5; }
    </style>
</head>
<body>
    <div class="sidebar">
        <button class="new-chat-button">
            <i class="fas fa-plus"></i>
            New chat
        </button>
    </div>

    <div class="main-content">
        <div class="chat-container" id="chatContainer">
            <div class="message">
                <div class="avatar gemini">
                    <i class="fas fa-robot"></i>
                </div>
                <div class="message-content">
                    Hi, I'm Gemini. How can I help you today?
                </div>
            </div>
        </div>

        <div class="input-container">
            <div class="input-group">
                <div class="input-wrapper">
                    <textarea id="prompt" placeholder="Message Gemini..." rows="1"></textarea>
                    <button id="submit" class="send-button">
                        <i class="fas fa-paper-plane"></i>
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        const promptInput = document.getElementById('prompt');
        const submitButton = document.getElementById('submit');
        const chatContainer = document.getElementById('chatContainer');
        const newChatButton = document.querySelector('.new-chat-button');

        // Auto-resize textarea
        promptInput.addEventListener('input', function() {
            this.style.height = 'auto';
            this.style.height = (this.scrollHeight) + 'px';
        });

        // New chat functionality
        newChatButton.addEventListener('click', () => {
            chatContainer.innerHTML = `
                <div class="message">
                    <div class="avatar gemini">
                        <i class="fas fa-robot"></i>
                    </div>
                    <div class="message-content">
                        Hi, I'm Gemini. How can I help you today?
                    </div>
                </div>
            `;
            promptInput.value = '';
            promptInput.style.height = 'auto';
        });

        // Handle form submission
        async function handleSubmit() {
            const prompt = promptInput.value.trim();
            if (!prompt) return;

            // Add user message
            chatContainer.innerHTML += `
                <div class="message">
                    <div class="avatar user">
                        <i class="fas fa-user"></i>
                    </div>
                    <div class="message-content">
                        ${prompt}
                    </div>
                </div>
            `;

            // Clear input
            promptInput.value = '';
            promptInput.style.height = 'auto';

            try {
                const response = await axios.post('http://localhost:3250/generate', {
                    prompt: prompt
                });

                // Add AI response
                chatContainer.innerHTML += `
                    <div class="message">
                        <div class="avatar gemini">
                            <i class="fas fa-robot"></i>
                        </div>
                        <div class="message-content">
                            ${response.data.response}
                        </div>
                    </div>
                `;
            } catch (error) {
                console.error('Error:', error);
                // Show error message
                chatContainer.innerHTML += `
                    <div class="message">
                        <div class="avatar gemini">
                            <i class="fas fa-robot"></i>
                        </div>
                        <div class="message-content" style="color: #ff4444;">
                            Sorry, there was an error processing your request.
                        </div>
                    </div>
                `;
            }

            // Scroll to bottom
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        submitButton.addEventListener('click', handleSubmit);
        promptInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                handleSubmit();
            }
        });
    </script>
</body>
</html>
