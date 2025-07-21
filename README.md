# 🎶 SonataAI — AI Platform for Piano Learning

📌 *Developed by Océane Kabasele, as part of a certifying project for the completion of a Master’s degree in Software Engineering (expected September 2025).*
⚠️ This is an academic, non-commercial project. **No external contributions are accepted.**
 
---

## 🌐 Overview

**SonataAI** is a microservices-based platform designed to analyze MIDI performances and provide AI-driven feedback and coaching.  
The system integrates real-time musical analysis, user authentication, session tracking, performance scoring, and payment handling.

The goal: deliver intelligent and personalized musical coaching using modern fullstack and AI technologies.

> The project is launched and managed via a `docker-compose` stack.

---

## 🧱 Microservice Architecture

| Service         | Description                                              | Tech Stack                      |
|-----------------|----------------------------------------------------------|----------------------------------|
| `frontend`      | Next.js web app for user interaction                     | Next.js, TypeScript, Tailwind   |
| `auth-service`  | Authentication + OAuth/JWT system                        | Node.js, Express, JWT           |
| `db-service`    | MongoDB access layer                                     | Node.js, Mongoose               |
| `ai-service`    | MIDI analysis, scoring, GPT API integration              | Node.js, OpenAI, Music libraries|
| `metrics`       | Monitoring and observability                             | Prometheus, Grafana             |
| `notifications` | Email/SMS/Push system for alerts and engagement          | Nodemailer, Twilio, Mailpit              |
| `payment`       | Subscription, invoicing and Stripe integration           | Node.js, Stripe, PayPal         |

> All services are Dockerized and orchestrated using `docker-compose`.

---

## 🚀 Getting Started

### 🔧 Prerequisites

- Docker + Docker Compose
- OpenAI Api key

### 📦 Installation

```bash
# Create a folder
mkdir SonataAI
cd SonataAI
# Clone the repositories
git clone [https://github.com/okabasele/sonataai.git](https://github.com/sonata-ai-piano-assistant/frontend.git)
Repeat the same for backend folders (authentication, database, ai, notifications, payment & metrics)

# Launch the entire stack
docker-compose up --build
````

## 📁 Repository Structure

```
SonataAI/
├── docker-compose.yml
├── frontend/
├── auth-service/
├── db-service/
├── ai-service/
├── metrics/
├── notifications/
├── payment/
└── README.md (this file)
```

---

Access points:

* 🖥️ Frontend UI: [http://localhost:3000](http://localhost:3000)
* 📬 Mailpit (Email debug): [http://localhost:8025](http://localhost:8025)
* 📈 Grafana:
    * default creds: admin/admin
* 🛠️ Prometheus: 

> You can configure env variables for each service in their respective `.env` files.

---

## 🔍 Project Diagrams

### 🧭 Architecture Overview

```mermaid
---
config:
  look: classic
  layout: elk
  theme: mc
---
flowchart TD
 subgraph Frontend["Frontend - NextJS App"]
        UI["NextJS User Interface"]
  end
 subgraph Auth["Authentication Service - NodeJS / Express"]
        AUTH["OAuth / OpenID Connect / JWT / Security"]
  end
 subgraph DBService["Database Service - NodeJS / Express"]
        DB["MongoDB: users, subscriptions, user_performance, partition"]
  end
 subgraph AIService["AI Service - NodeJS / Express"]
        AI["MIDI Analysis + AI / LLM / TTS / Score Comparison"]
  end
 subgraph Metrics["Monitoring Service - NodeJS / Express"]
        METRICS["Prometheus Exporter / Grafana Dashboard"]
  end
 subgraph Notification["Notification Service - NodeJS / Express"]
        NOTIF["Emails (Nodemailer), SMS (Twilio), Push"]
  end
 subgraph Paiement["Payment Service - NodeJS / Express"]
        PAY["Stripe / PayPal API / Subscriptions and Billing"]
  end
    UI --> AUTH & AI & DB & PAY
    AUTH --> DB & NOTIF & METRICS
    AI --> DB & METRICS
    PAY --> DB & NOTIF & METRICS
    NOTIF --> UI & METRICS
    DB --> METRICS
```

---

## 🧠 AI Pipeline

```mermaid
flowchart LR
    U[User] -->|Create session| S[Session]
    U -->|Play piano| P[MIDI Performance]
    U -->|Ask question| AI[AI Assistant]
    S -->|Session info| AI
    P -->|Performance data| AI
    AI -->|Uses tools| Tools[Analysis & Retrieval]
    Tools -->|Fetches| S
    Tools -->|Fetches| P
    AI -->|Responds| U
```

---

## 📊 UML Entities

```mermaid
classDiagram
    class User {
        ObjectId _id
        String firstname
        String lastname
        String username
        String email
        String password
        Phone phone
        OAuthAccount[] oauthAccounts
        Boolean isEmailVerified
        Boolean isPhoneVerified
        Date signupDate
        NotificationSettings notifications
    }
    class Subscription {
        ObjectId _id
        ObjectId user
        String stripeSubscriptionId
        String plan
        String status
        Date currentPeriodEnd
        Date createdAt
    }
    class Session {
        ObjectId _id
        ObjectId user
        ObjectId reference
        ObjectId[] performances
        String threadId
        Date startedAt
        Date endedAt
    }
    class Reference {
        ObjectId _id
        String name
        Section sections
    }
    class Section {
        MidiNote[] intro
        MidiNote[] verse
        MidiNote[] chorus
        MidiNote[] bridge
        MidiNote[] outro
    }
    class MidiNote {
        Number note
        Number velocity
        Number time
    }
    class Performance {
        ObjectId _id
        Date startedAt
        Date endedAt
        String section
        MidiNote[] midiNotes
        ObjectId user
        ObjectId session
        Feedback feedback
    }
    class Feedback {
        Number score
        String comments
        Mixed details
    }
    class Notification {
        ObjectId _id
        String userId
        String type
        String channel
        String to
        String content
        String status
        String error
        Boolean consent
        String unsubscribeToken
        Object meta
        Date createdAt
        Date updatedAt
    }
    User "1" -- "*" Subscription : has
    User "1" -- "*" Session : has
    User "1" -- "*" Performance : has
    Session "1" -- "*" Performance : includes
    Session "1" -- "1" Reference : for
    Reference "1" -- "1" Section : has
    Performance "1" -- "1" Feedback : has
    User "1" -- "*" Notification : receives
```

---

## 🧪 Testing & Monitoring

Prometheus and Grafana provide service-level dashboards.

Future additions:

* Unit testing in each backend services
* Cypress tests for frontend
* Load testing for AI & auth
* Linting and CI/CD pipelines (GitHub Actions)

---

## 📜 License

MIT © Océane Kabasele 2025
