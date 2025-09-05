# Astral Plane API Documentation
*Live Forever, Learn Forever: Personalized Agent*

## Overview
Astral Plane is an infrastructure platform that creates personalized AI agents through continuous learning. The system provides voice and text conversation capabilities with on-the-fly model fine-tuning, enabling agents that evolve and adapt to individual users over time.

## Voice Mode API

### Speech to Text → AI Response
Converts audio input to text, processes it through AI, and chains to the next device.

**URL:** `http://YOUR_SERVER_ADDRESS:YOUR_PORT/~speech-to-text@1.0/transcribe/infer~wasi-nn@1.0?model-id=gemma&session_id=johncena --data-binary @audiofile.wav`  
**Method:** `POST`

**Request:**
```bash
curl -X POST "http://YOUR_SERVER_ADDRESS:YOUR_PORT/~speech-to-text@1.0/transcribe/infer~wasi-nn@1.0?model-id=gemma&session_id=johncena" \
  --data-binary @1.wav
```

**Parameters:**
- `model-id` (optional): AI model identifier
- `session_id` (required): Session identifier for conversation continuity

**Input:** Binary audio file (WAV/MP3)  
**Output:** AI response text with session context

### Text to Speech Generation
Generates audio from AI response text.

**URL:** `http://YOUR_SERVER_ADDRESS:YOUR_PORT/~text-to-speech@1.0/generate?session_id=johncena -d '{"result": "text content"}'`  
**Method:** `POST`

**Request:**
```bash
curl -X POST 'http://YOUR_SERVER_ADDRESS:YOUR_PORT/~text-to-speech@1.0/generate?session_id=johncena' \
  -H "Content-Type: application/json" \
  -d '{"result": "Hello! I understand what you need. How can I help you today?"}' \
  --output response.wav
```

**Parameters:**
- `session_id` (required): Session identifier for voice consistency

**Input:**
```json
{
  "result": "Text to convert to speech"
}
```

**Output:** Binary audio file (WAV format)

## Text-Based Conversations API

### Start Conversation
Begin a text-based conversation with optional custom model.

**URL:** `http://YOUR_SERVER_ADDRESS:YOUR_PORT/~wasi-nn@1.0/infer?lora_id=my_training_session&session_id=user_123 -d '{"transcription": "your message"}'`  
**Method:** `POST`

**Request:**
```bash
curl -X POST "http://YOUR_SERVER_ADDRESS:YOUR_PORT/~wasi-nn@1.0/infer?lora_id=my_training_session&session_id=user_123" \
  -H "Content-Type: application/json" \
  -d '{"transcription": "who are you?"}'
```

**Parameters:**
- `lora_id` (optional): Custom fine-tuned model identifier
- `session_id` (required): Session identifier for conversation memory

**Input:**
```json
{
  "transcription": "Your message or question here"
}
```

**Output:**
```json
{
  "result": "AI response text",
  "session_id": "user_123",
  "transcription": "Your original message"
}
```

## Model Fine-Tuning API

Fine-tune AI models on-the-fly with custom conversation data to create personalized responses.

### Train Custom Model
Train a personalized model using your conversation examples.

**URL:** `http://YOUR_SERVER_ADDRESS:YOUR_PORT/~training@1.0/train?session_id=my_training_session&num_epochs=10 -d @training_data.json`  
**Method:** `POST`

**Request:**
```bash
curl -X POST "http://YOUR_SERVER_ADDRESS:YOUR_PORT/~training@1.0/train?session_id=my_training_session&num_epochs=10" \
  -H "Content-Type: application/json" \
  -d @training_data.json
```

**Parameters:**
- `session_id` (required): Unique identifier for your custom model
- `num_epochs` (optional): Training intensity (default: varies by model)

**Input:** JSON file with conversation examples
```json
[
  {
    "input": "What's your favorite color?",
    "output": "I really enjoy deep blues and emerald greens."
  },
  {
    "input": "Tell me about yourself",
    "output": "I'm an AI assistant trained to be helpful and conversational."
  }
]
```

**Output:**
```json
{
  "status": "success",
  "adapter_path": "runs/my_training_session/finetune_lora",
  "session_id": "my_training_session"
}
```

### Convert Model for Use
Convert the trained model to optimized format for conversations.

**URL:** `http://YOUR_SERVER_ADDRESS:YOUR_PORT/~training@1.0/convert`  
**Method:** `POST`

**Request:**
```bash
curl -X POST "http://YOUR_SERVER_ADDRESS:YOUR_PORT/~training@1.0/convert?session_id=my_training_session" \
  -H "Content-Type: application/json"
```

**Parameters:**
- `session_id` (required): Session ID from the training step

**Output:**
```json
{
  "status": "success",
  "gguf_path": "runs/my_training_session/lora_to_gguf/lora_adapter.gguf",
  "session_id": "my_training_session"
}
```

### Download Custom Model
Download your trained model file for backup or sharing.

**URL:** `http://YOUR_SERVER_ADDRESS:10000/~training@1.0/download`  
**Method:** `POST`

**Request:**
```bash
curl -X POST "http://YOUR_SERVER_ADDRESS:10000/~training@1.0/download?session_id=my_training_session" \
  --output my_custom_model.gguf
```

**Parameters:**
- `session_id` (required): Session ID of your trained model

**Output:** Binary model file (.gguf format)

## Data Formats

### Audio Files
- **Supported formats:** WAV, MP3
- **Recommended:** WAV for best quality

### Training Data
Provide conversation examples as JSON array with input/output pairs. The model learns to respond in your specified style and personality.

### Session Management
All endpoints require a `session_id` parameter to maintain conversation context and model associations. Use consistent session IDs to continue conversations or reference trained models.