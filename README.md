# 🏥 Health Assistant — Multi-LLM Medical Helper

An AI-powered application that integrates multiple LLMs to help sick people find the right medical care.

## Overview

This application uses **two specialized LLM agents** that work together:

1. **Symptom Collector Agent (LLM 1)** — Interviews the user about their symptoms, age, gender, and location through a conversational interface.
2. **Doctor Finder Agent (LLM 2)** — Takes the collected patient information and recommends suitable hospitals, clinics, and doctors in the user's location.

## Tech Stack

- **Java 21**
- **Spring Boot 3.4.3**
- **Spring AI** (OpenAI integration)
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
                    └──────── OpenAI API ──────────┘
```

### Flow

1. User sends a message via the chat interface
2. `ChatOrchestrationService` routes it to **Agent 1** (Symptom Collector)
3. Agent 1 asks about symptoms, age, gender, and location
4. Once all info is gathered, Agent 1 produces a structured summary
5. The orchestrator automatically forwards the summary to **Agent 2** (Doctor Finder)
6. Agent 2 recommends healthcare providers near the user's location
7. User can ask follow-up questions to Agent 2

## Prerequisites

- **Java 21** or later
- **Maven 3.8+**
- **OpenAI API key** (get one at https://platform.openai.com/api-keys)

## Getting Started

### 1. Set your OpenAI API key

```bash
export OPENAI_API_KEY=sk-your-api-key-here
```

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

Navigate to: **http://localhost:8080**

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

## Disclaimer

⚠️ This application is for **demonstration purposes only**. Always consult a qualified healthcare professional for medical concerns.
