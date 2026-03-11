# 🏥 Health Assistant — Multi-LLM Medical Helper

An AI-powered application that integrates multiple LLM agents (powered by **Ollama**) to help sick people find the right medical care.

## Overview

This application uses **two specialized LLM agents** that work together, both running locally via [Ollama](https://ollama.com):

1. **Symptom Collector Agent (LLM Agent 1)** — Interviews the user about their symptoms, age, gender, and location through a conversational interface.
2. **Doctor Finder Agent (LLM Agent 2)** — Takes the collected patient information and recommends suitable hospitals, clinics, and doctors in the user's location.

## Tech Stack

- **Java 21**
- **Spring Boot 3.4.3**
- **Spring AI 1.0.0-M6** (Ollama integration)
- **Ollama** with **llama3.2** model (local LLM)
- **Maven** (build tool)
- **HTML/CSS/JS** (chat UI)

## Architecture

```
User <-> ChatController <-> ChatOrchestrationService
                                  |
                    ┌─────────────┼─────────────┐
                    ▼                             ▼
          SymptomCollectorAgent           DoctorFinderAgent
             (LLM Agent 1)                 (LLM Agent 2)
                    │                             │
                    └───── Ollama (llama3.2) ──────┘
```

### Flow

1. User sends a message via the chat interface
2. `ChatOrchestrationService` routes it to **Agent 1** (Symptom Collector)
3. Agent 1 asks about symptoms, age, gender, and location
4. Once all info is gathered, Agent 1 produces a structured JSON summary
5. The orchestrator automatically forwards the summary to **Agent 2** (Doctor Finder)
6. Agent 2 recommends healthcare providers near the user's location
7. User can ask follow-up questions to Agent 2

## Prerequisites

- **Java 21** or later
- **Maven 3.8+**
- **Ollama** installed and running locally (see [ollama.com](https://ollama.com) for installation)

## Getting Started

### 1. Install and start Ollama

Download and install Ollama from [https://ollama.com](https://ollama.com), then pull the required model:

```bash
ollama pull llama3.2
```

Make sure Ollama is running (it typically starts automatically after installation and listens on `http://localhost:11434`).

### 2. Build the project

```bash
./mvnw clean package
```

Or with Maven installed:

```bash
mvn clean package
```

### 3. Run the application

```bash
./mvnw spring-boot:run
```

### 4. Open the chat UI

Navigate to: **http://localhost:8081**

## Configuration

The Ollama connection is configured in `src/main/resources/application.yml`:

```yaml
spring:
  ai:
    ollama:
      base-url: http://localhost:11434
      chat:
        model: llama3.2
        options:
          temperature: 0.7
```

You can change the model to any Ollama-supported model (e.g., `mistral`, `llama3.1`, `gemma2`) by updating the `model` property and pulling the model with `ollama pull <model-name>`.

## API Endpoints

### Send a chat message

```
POST /api/chat
Content-Type: application/json

{ "sessionId": "optional-session-id", "message": "I have a headache and fever" }
```

### Reset a session

```
DELETE /api/chat/{sessionId}
```

## Conversation States

| State | Description |
|-------|-------------|
| `COLLECTING_SYMPTOMS` | Agent 1 is interviewing the patient |
| `FINDING_DOCTORS` | Agent 2 is recommending healthcare providers |
| `COMPLETE` | Conversation complete (can still ask follow-ups) |

## Project Structure

```
src/main/java/com/demoapp/healthassistant/
├── HealthAssistantApplication.java
├── controller/ChatController.java
├── service/
│   ├── ChatOrchestrationService.java
│   ├── SymptomCollectorAgent.java
│   └── DoctorFinderAgent.java
├── model/
│   ├── ChatRequest.java, ChatResponse.java
│   ├── ChatMessage.java, ConversationState.java
│   └── UserSession.java
└── exception/GlobalExceptionHandler.java
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `Connection refused` on port 11434 | Make sure Ollama is running (`ollama serve`) |
| Model not found | Run `ollama pull llama3.2` to download the model |
| Slow responses | Local LLM performance depends on your hardware; consider using a smaller model |

## Disclaimer

⚠️ This application is for **demonstration purposes only**. It does not provide real medical advice. Always consult a qualified healthcare professional for medical concerns.
