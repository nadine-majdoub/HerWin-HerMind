# HERmind – AI-Powered Well-being and Academic Support

HERmind is a mobile application developed to enhance the well-being and academic experience of university students and young graduates in Tunisia. It brings together personal reflection tools, study assistance, and an optional AI-powered companion within a single platform.

The application supports French, Arabic, and Tunisian Derja, including Arabizi, making its features more accessible to students with different language preferences.

> **Prototype Notice:** HERmind is a hackathon project intended for demonstration purposes. It is not a healthcare or mental-health solution and cannot replace professional assistance, provide medical diagnoses, or handle emergencies. Real sensitive personal information should not be used during demonstrations.

## The Problem

University life can expose students to various challenges, including academic stress, emotional pressure, social isolation, and difficulties finding suitable support.

Existing solutions often address academic organization and personal well-being separately. Furthermore, generic digital assistants may not adequately consider students' linguistic preferences or specific situations.

HERmind aims to bring these aspects together by providing an accessible environment for personal reflection, academic organization, and AI-assisted support.

## Main Features

HERmind is organized around several complementary modules that address different aspects of students' daily lives.

### 1. Mood Tracking and Personal Reflection

- Record daily emotional states through mood check-ins.
- Add personal notes for private reflection.
- Monitor small daily actions and personal progress directly on the device.

### 2. AI Companion

An optional conversational assistant designed to provide different types of interaction according to the user's needs:

- Active listening and supportive conversations.
- Suggestions for practical and achievable actions.
- Guided breathing and relaxation exercises.

### 3. A7kili – Listening and Discovery

A dedicated space focused on supportive listening and relaxation.

- Offers an interactive listening experience.
- Provides optional YouTube search suggestions related to podcasts and music.
- Supports multilingual interactions.

The application provides search phrases rather than verifying the relevance or safety of individual YouTube results.

### 4. Réussite – Academic Assistance

A study-support module integrating AI capabilities to facilitate learning and academic organization.

- Generate AI-assisted study schedules and learning plans.
- Provide simplified explanations of academic subjects.
- Create interactive quizzes presented one question at a time.
- Offer practical recommendations and motivational guidance.

### 5. Interactive Avatar

An animated companion that enhances user interaction through device capabilities.

- Animated avatar interface.
- Optional voice recording and input.
- Audio transcription through the backend.
- Spoken AI responses using the device's text-to-speech functionality.
- Local camera preview for a more interactive experience.

### 6. Planning and Personal Journey

Tools that help users organize their activities and review their personal progress.

- Schedule academic and personal events.
- Access previous mood check-ins.
- Review personal activities and reflections.

### 7. HerCircle – Community Interface

A community-inspired module designed to represent a space for student interaction and connection.

- Community-style interface.
- Demonstration circles and member statistics.
- Visual elements illustrating a collaborative student environment.

**Implementation Details:** HerCircle is currently a demonstration interface rather than a live social network. Some visual indicators and statistics are illustrative. Mood-related recommendations are generated using local rule-based logic and do not constitute AI-generated psychological assessments.

## AI Architecture and Integration

The AI functionality is implemented through a dedicated backend service. The mobile application communicates with the HERmind API, which handles requests and connects to the external Groq services.

Direct communication between the mobile application and Groq is not used.

### System Communication Flow

```text
          Expo / React Native Application
                       |
             ---------------------
             |                   |
        JSON Requests       Authenticated Audio
             |                   |
             v                   v
             HERmind FastAPI Backend
                       |
                ----------------
                |              |
                v              v
             Groq Chat      Groq Whisper
             Language       Audio
              Model       Transcription
```

### AI Implementation

The backend is responsible for managing AI requests and securely handling the Groq API credentials.

- `HER_GROQ_API_KEY` is configured exclusively on the backend.
- The chat service uses Groq's OpenAI-compatible API.
- The default conversational model is `qwen/qwen3.8-27b`.
- Audio transcription is handled by `whisper-large-v3-turbo`.
- Dedicated prompts adapt the AI responses to the different application modules.

These prompts cover emotional listening, practical recommendations, relaxation, A7kili interactions, academic planning, tutoring, quizzes, and motivation.

For Tunisian Derja, the prompts instruct the language model to follow the user's preferred writing system, whether Arabic characters or Latin-based Arabizi. However, this approach does not guarantee perfect dialect accuracy.

### Voice Interaction Workflow

The avatar's voice functionality follows this process:

1. Audio is recorded through the mobile application.
2. The recording is transmitted to the FastAPI backend.
3. Groq Whisper processes the audio and produces a text transcription.
4. The resulting text can be submitted to the conversational AI service.
5. The generated response can be played using the device's native text-to-speech engine.

The camera preview associated with the avatar remains entirely local to the device and is not transmitted to the AI service.

### AI Service Availability

Access to conversational AI and voice transcription depends on a valid Groq API key and an operational connection to the backend.

When these requirements are not met, the application reports that the corresponding service is unavailable instead of generating simulated AI responses.

Features that operate locally remain accessible without an active Groq connection.

Server-side conversation history is disabled by default and requires separate user consent. Audio recordings are not stored by HERmind, while mood notes and planning information remain locally stored within the application.

## Technology Stack

| Layer | Technologies |
|---|---|
| Mobile Application | Expo SDK 57, React Native 0.86, React 19 |
| Frontend Language | TypeScript |
| Navigation | Expo Router |
| Device Integration | Expo Audio, Camera, SecureStore, Speech, FileSystem |
| Backend Framework | Python 3.11+, FastAPI |
| Backend Components | Pydantic Settings, SQLAlchemy Async |
| Artificial Intelligence | Groq Chat Completions, Whisper |
| Database | SQLite (default), PostgreSQL (optional) |
| Backend Testing | Pytest |

## Installation and Execution

### Prerequisites

The following tools and services are required to set up the development environment:

- Node.js and npm.
- Python 3.11 or a more recent version.
- [uv](https://docs.astral.sh/uv/) for Python dependency management.
- Expo Go for physical-device testing or an Android/iOS simulator.
- A Groq API key to enable conversational AI and audio transcription.

### 1. Install the Mobile Dependencies

From the repository's root directory, execute:

```bash
npm ci
```

### 2. Set Up and Launch the Backend

Open a terminal and move into the backend directory:

```bash
cd backend
```

Create the local environment configuration file using PowerShell:

```powershell
Copy-Item .env.example .env
```

Edit `backend/.env` and provide your Groq API key:

```env
HER_GROQ_API_KEY=your_groq_api_key
```

The API key can be generated through the [Groq Console](https://console.groq.com/keys).

For security purposes, this credential must remain in the backend environment. It must never be included in an `EXPO_PUBLIC_` variable or committed to the repository.

The default configuration relies on SQLite, so PostgreSQL is not mandatory for local execution.

Install the backend dependencies:

```bash
uv sync
```

Start the FastAPI server:

```bash
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Execute these commands from the `backend` directory.

The API health-check endpoint is accessible at:

```text
http://localhost:8000/health
```

### 3. Configure and Start the Mobile Application

From the repository root, generate the local environment file:

```powershell
Copy-Item .env.example .env.local
```

Configure `EXPO_PUBLIC_API_URL` according to the environment in which the application will run.

**For web browsers or an Android emulator running on the same computer:**

```env
EXPO_PUBLIC_API_URL=http://localhost:8000
```

**For a physical mobile device:**

```env
EXPO_PUBLIC_API_URL=http://<computer-LAN-IP>:8000
```

For example:

```env
EXPO_PUBLIC_API_URL=http://192.168.1.20:8000
```

When using a physical device, ensure that:

- The computer and smartphone are connected to the same Wi-Fi network.
- The computer's local IP address is correctly configured.
- Port 8000 is permitted by the computer's firewall.

The development API should not be exposed directly to the public internet.

Launch the Expo development server:

```bash
npm run start -- --go --lan
```

Scan the displayed QR code using Expo Go to access the application.

For browser-based testing, execute:

```bash
npm run web
```

If the environment configuration changes while Expo is running, restart the development server to load the new settings.

## Local Operation and AI Dependencies

HERmind can be launched and explored without configuring a Groq API key.

However, AI-powered conversations and voice transcription require both a valid backend configuration and an active connection between the mobile application and the API server.

When the AI services are unavailable, the backend returns an appropriate service-unavailable response instead of providing fabricated results. Features that rely exclusively on local processing remain functional.

## Testing and Validation

Several commands are available to verify the application's implementation and maintain code quality.

**Backend tests**

From the `backend` directory:

```bash
uv run pytest
```

**Frontend linting**

From the repository root:

```bash
npm run lint
```

**TypeScript compilation check**

```bash
npx tsc --noEmit
```

## Privacy, Security and Production Considerations

HERmind has been developed as a prototype, with particular attention to separating local application data from backend-based AI processing.

The current implementation includes the following privacy-related mechanisms:

- Mood records and planning information are stored locally on the device.
- Server-side conversation storage is disabled by default and requires separate consent.
- Uploaded audio recordings are not retained by HERmind.
- The avatar's camera preview is processed locally.
- Groq credentials are maintained on the backend rather than in the mobile application.

### Current Limitations and Future Security Requirements

Despite these measures, the prototype does not yet implement all the protections required for a production deployment.

In particular:

- Server-stored conversation messages are currently saved as plaintext in the configured database.
- The local development API is not hardened for production environments.
- Additional authentication and access-control mechanisms are required.
- Secure communication through TLS, encrypted storage, and database migrations should be implemented.
- Rate limiting, monitoring, backups, and data-retention policies must be established.
- A comprehensive safety-review process should be introduced for AI interactions.

These improvements are necessary before deploying HERmind in a real-world environment involving sensitive user information.

For further technical information about the backend implementation, refer to [`backend/README.md`](backend/README.md).


---

**HERmind – Supporting student well-being and academic success through accessible technology and AI.**
