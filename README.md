# ErweckeKIp

const messagesDiv = document.getElementById('messages');
const userInput = document.getElementById('user-input');
const sendButton = document.getElementById('send-button');

// Define multiple AI personalities
const ais = [
  { 
    name: 'ChatGPT', 
    // Diese Funktion simuliert eine API-Anfrage
    response: async (userMsg) => {
      return new Promise(resolve => {
        // Simuliert eine Netzwerkverzögerung von 1500ms (1.5 Sekunden)
        setTimeout(() => {
          const possibleResponses = [
            `ChatGPT: Ich habe zu "${userMsg}" recherchiert und folgendes gefunden...`,
            `ChatGPT: Deine Frage zu "${userMsg}" ist interessant. Hier ist eine detaillierte Antwort.`,
            `ChatGPT: Basierend auf den verfügbaren Daten zu "${userMsg}" kann ich sagen, dass...`,
            `ChatGPT: Gerne helfe ich dir bei "${userMsg}". Was genau möchtest du wissen?`
          ];
          const randomIndex = Math.floor(Math.random() * possibleResponses.length);
          resolve(possibleResponses[randomIndex]);
        }, 1500); 
      });
    }
  },
  { name: 'Grok', response: (userMsg) => `Grok: "${userMsg}" – darüber können wir reden. Was möchtest du wissen?` },
  { name: 'Bard', response: (userMsg) => `Bard: Ja, "${userMsg}" ist ein gutes Thema. Hier ist meine Perspektive.` }
];

function addMessage(text, sender) {
  const messageElement = document.createElement('div');
  messageElement.classList.add('message', `${sender}-message`);
  messageElement.textContent = text;
  messagesDiv.appendChild(messageElement);
  messagesDiv.scrollTop = messagesDiv.scrollHeight; // Scroll to bottom
}

async function handleSendMessage() { // Funktion ist jetzt async, um await nutzen zu können
  const messageText = userInput.value.trim();
  if (messageText !== '') {
    addMessage(messageText, 'user');
    userInput.value = '';

    // Simulate multiple AI responses
    for (let i = 0; i < ais.length; i++) {
      const ai = ais[i];
      const delay = 500 + (i * 700); // Stagger delays slightly for each AI

      // Wenn die AI eine async-Funktion hat (wie ChatGPT), warten wir auf die Antwort
      if (ai.response.constructor.name === 'AsyncFunction') {
        setTimeout(async () => {
          const aiResponse = await ai.response(messageText);
          addMessage(aiResponse, 'ai');
        }, delay);
      } else {
        // Für synchrone AI-Antworten
        setTimeout(() => {
          addMessage(ai.response(messageText), 'ai');
        }, delay);
      }
    }
  }
}

sendButton.addEventListener('click', handleSendMessage);

userInput.addEventListener('keypress', (event) => {
  if (event.key === 'Enter') {
    handleSendMessage();
  }
});

// Initial AI message (or introduction from multiple AIs)
window.onload = () => {
  addMessage("Willkommen! Wir sind ein Team von KIs, bereit zum Chatten. Was beschäftigt dich?", 'ai');
};