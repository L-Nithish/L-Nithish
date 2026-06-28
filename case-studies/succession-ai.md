# Case Study: Succession AI — Real-Time WebSocket Interview Sandbox

## 1. Executive Summary
**Succession AI** (InterviewAce-AI) is an event-driven developer sandbox and real-time AI mock interview coaching workspace. It provides candidates with dynamic, context-aware coding challenges and conversational feedback, simulating a live technical interview.

* **Live Demo:** [https://succession-ai-s4hp.vercel.app](https://succession-ai-s4hp.vercel.app)
* **GitHub Repository:** [https://github.com/L-Nithish/Succession-AI](https://github.com/L-Nithish/Succession-AI)

---

## 2. System Architecture
The platform utilizes an event-driven system design to maintain full-duplex communication channels between the client and server:

```
┌────────────────────────────────────────────────────────┐
│                   Next.js 14 Client                    │
│  - In-Browser Monaco Code Editor                       │
│  - SockJS / STOMP WebSocket Client                     │
└──────────────────────────▲─────────────────────────────┘
                           │
                 WebSocket (STOMP)
                Full-Duplex Channel
                           │
┌──────────────────────────▼─────────────────────────────┐
│                 Spring Boot API Server                 │
│  - WebSocket Message Broker (STOMP)                    │
│  - In-Memory Question/Session Manager                  │
│  - OpenAI API Integration Service                      │
└──────────────────────────┬─────────────────────────────┘
                           │
                       API Call
                           │
┌──────────────────────────▼─────────────────────────────┐
│                       OpenAI API                       │
│  - Resume Parsing & Custom Question Generator          │
└────────────────────────────────────────────────────────┘
```

---

## 3. Engineering Challenges & Solutions

### Challenge 1: High Latency in Conversational AI
Traditional HTTP request-response polling is too slow and resource-intensive for live interview coaching. Waiting for a response breaks the flow and immersion of a real interview.
* **Solution:** Implemented **STOMP over WebSockets**. By establishing a persistent, bi-directional TCP connection between the Next.js client and the Spring Boot backend, message transmission latency was reduced to **under 10ms**, with AI processing happening asynchronously.

### Challenge 2: Dynamic Question Generation Based on Experience
Static interview questions fail to evaluate a candidate’s actual resume and skill level.
* **Solution:** Integrated the **OpenAI Chat Completion API** with structured JSON output. When a candidate uploads their resume, the backend parses the text, extracts key technologies, and dynamically generates tailored technical questions in real-time.

---

## 4. Technical Decisions & Key Features

* **In-Browser Syntax Validation:** Implemented an interactive coding sandbox that provides immediate syntax checks and validation before the candidate submits their code.
* **SockJS Fallback:** Configured SockJS within the Spring Boot WebSocket configuration to ensure compatibility with older browsers or networks that do not support raw WebSockets.
* **Stateless Session Management:** User interview states are managed in-memory during the session and backed by a PostgreSQL database for historical analytics.

---

## 5. Performance Metrics
* **WebSocket Message Delivery:** `<10ms` latency for round-trip chat updates.
* **Resume Parsing & Generation:** Optimized prompts to reduce OpenAI generation times by `40%`, delivering personalized questions in under `1.8 seconds`.
* **Coding Sandbox Execution:** Immediate local validation, reducing unnecessary compilation requests to the backend server.
