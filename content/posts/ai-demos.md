---
title: "Wisdom Tree - Ancient Knowledge & Guidance"
weight: 0
tags: ["wisdom", "guidance", "text", "javascript"]
categories: ["demo"]
---

# Ancient Wisdom Computing

Experience the wisdom of ages through an ancient tree that has observed the world for centuries.

## Wisdom Tree

Ask the ancient Wisdom Tree about your thoughts and questions:

<div id="chatbot-demo">
  <div id="chat-history" style="height: 200px; overflow-y: auto; border: 1px solid #666; border-radius: 4px; padding: 10px; background: #f8f9fa; margin-bottom: 10px; color: #333;">
    <div style="color: #666; font-style: italic;">The Wisdom Tree is awakening... Please wait.</div>
  </div>
  <div style="margin-bottom: 10px;">
    <input type="text" id="chat-input" placeholder="Share your thoughts or ask for wisdom..." style="width: 75%; padding: 10px; border: 1px solid #666; border-radius: 4px; color: #333; background: #fff;" disabled>
    <button id="chat-send" style="padding: 10px 20px; border: 1px solid #666; border-radius: 4px; background: #228B22; color: #fff; cursor: pointer; margin-left: 8px;" disabled>Ask</button>
  </div>
</div>

<script type="module">
import { pipeline, env } from 'https://cdn.jsdelivr.net/npm/@xenova/transformers@2.17.2';

env.allowRemoteModels = true;
env.allowLocalModels = false;

let textGenerator;

async function initializeModels() {
  try {
    document.getElementById('chat-history').innerHTML = '<div style="color: #666; font-style: italic;">The ancient Wisdom Tree is awakening...</div>';
    textGenerator = await pipeline('text-generation', 'Xenova/gpt2', {
      max_new_tokens: 100,
      temperature: 0.7,
      do_sample: true,
    });
    
    document.getElementById('chat-input').disabled = false;
    document.getElementById('chat-send').disabled = false;
    document.getElementById('chat-history').innerHTML = '<div style="color: #228B22; font-weight: bold;">🌳 The Wisdom Tree has awakened. Share your thoughts, seeker.</div>';
    
  } catch (error) {
    console.error('Error loading models:', error);
    document.getElementById('chat-history').innerHTML = '<div style="color: #dc3545;">The Wisdom Tree could not awaken. Please refresh the page.</div>';
  }
}

const chatHistory = document.getElementById('chat-history');
const chatInput = document.getElementById('chat-input');
const chatSend = document.getElementById('chat-send');

function displayQA(question, answer) {
  chatHistory.innerHTML = '';
  
  const qDiv = document.createElement('div');
  qDiv.style.margin = '8px 0';
  qDiv.style.padding = '4px 0';
  qDiv.innerHTML = `<strong>You:</strong> ${question}`;
  chatHistory.appendChild(qDiv);
  
  const aDiv = document.createElement('div');
  aDiv.style.margin = '8px 0';
  aDiv.style.padding = '4px 0';
  aDiv.innerHTML = `<strong>🌳 Wisdom Tree:</strong> ${answer}`;
  chatHistory.appendChild(aDiv);
}

chatSend.onclick = async function() {
  const userMsg = chatInput.value.trim();
  if (!userMsg || !textGenerator) return;
  
  if (userMsg.length > 300) {
    alert('Your thoughts are too lengthy. Please keep them under 300 characters.');
    return;
  }
  
  chatSend.disabled = true;
  chatSend.textContent = 'Pondering...';
  chatHistory.innerHTML = '<div style="color: #666; font-style: italic;">The Wisdom Tree is contemplating your words...</div>';
  
try {
    const prompt = `You are an ancient wisdom tree. Speak very briefly and profoundly. Use few words with deep meaning. Be mysterious and cryptic. Sound deep and contemplative. A seeker asks: "${userMsg}"\n\nWisdom Tree whispers:`;
    
    const result = await textGenerator(prompt, {
      max_new_tokens: 500,
      temperature: 0.8,
      do_sample: true,
      pad_token_id: 50256,
      eos_token_id: 50256,
      repetition_penalty: 1.2,
      top_p: 0.85,
      top_k: 40
    });
    
    let response = result[0].generated_text.replace(prompt, '').trim();
    
    const lines = response.split('\n').filter(line => line.trim());
    if (lines.length > 0) {
      response = lines[0].trim();
    }
    
    response = response.replace(/^(Answer:|Question:|A:|Q:)/i, '').trim();
    
    if (response.length > 15 && !response.match(/[.!?]$/)) {
      const sentences = response.split(/([.!?]+)/);
      if (sentences.length > 2) {
        let completeResponse = '';
        for (let i = 0; i < sentences.length - 1; i += 2) {
          if (sentences[i].trim() && sentences[i + 1]) {
            completeResponse += sentences[i].trim() + sentences[i + 1];
          }
        }
        if (completeResponse.length > 15) {
          response = completeResponse.trim();
        }
      }
    }
    
    if (response.length < 15 || response === '') {
      response = "The winds whisper that your question requires deeper reflection. Perhaps ask in another way, seeker.";
    }
    
    if (response.length > 400) {
      response = response.substring(0, 397) + '...';
    }
    
    displayQA(userMsg, response);
    
  } catch (error) {
    console.error('Generation error:', error);
    displayQA(userMsg, "The ancient roots stir with confusion. Please ask your question again, seeker.");
  }
  
  chatSend.disabled = false;
  chatSend.textContent = 'Ask';
};

chatInput.addEventListener('keydown', function(e) {
  if (e.key === 'Enter' && !chatSend.disabled) chatSend.onclick();
});

initializeModels();
</script>

<style>
#chatbot-demo {
  border: 1px solid #ddd;
  padding: 20px;
  margin: 24px 0;
  border-radius: 6px;
  background: #fff;
  color: #333;
}

button:hover {
  opacity: 0.85;
  transform: translateY(-1px);
}

input[type="text"] {
  font-family: inherit;
  font-size: 14px;
}

button {
  font-family: inherit;
  font-size: 14px;
  font-weight: 500;
}

#chat-history div {
  color: #333;
  line-height: 1.4;
}
</style>