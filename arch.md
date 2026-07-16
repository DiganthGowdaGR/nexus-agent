# CoComputer: System Architecture Specification

This document provides a detailed specification of the CoComputer (Nexus Agent) system architecture, tracing the pathways of frontend user interaction, the backend orchestrator, the multi-agent system, the E2B sandbox, and external integrations.

---

## 🗺️ 1. High-Level Component Topology

Below is the layout of the system components and their networks.

![System Architecture Diagram](docs/cocomputer_architecture_diagram.png)

```mermaid
graph TB
    subgraph Client ["🖥️ Client Space (Next.js Application)"]
        UI["App Shell (React 19 / TypeScript)"]
        Chat["Unified Chat Console"]
        Desktop["VNC Desktop Viewer (noVNC / iframe)"]
        Audio["PCM Audio Capture (Mic Handler)"]
        FB_Client["Firebase Authentication SDK"]
        WS_Client["WebSocket client (useWebSocket)"]
    end

    subgraph GCP ["☁️ Google Cloud Platform (Nexus Services)"]
        CR_FE["Cloud Run: nexus-frontend"]
        CR_BE["Cloud Run: nexus-agent (FastAPI)"]
        FB_Auth["Firebase Auth Service (ID Token Verification)"]
        FirestoreDB[("Firestore Database<br/>• users<br/>• sessions<br/>• messages<br/>• user_settings")]
        VertexAI["Vertex AI Service (Gemini Models)"]
        SecretManager["Google Secret Manager (System Secrets)"]
    end

    subgraph SandboxEnv ["📦 E2B Execution Sandbox"]
        E2B["E2B Desktop API Container (Ubuntu VM)"]
        VNC["VNC Server & X11 Session"]
        Browser["Sandboxed Google Chrome"]
        Terminal["Sandboxed Bash Shell / OS filesystem"]
    end

    subgraph External ["🌐 External Providers & Integrations"]
        KiloGateway["Kilo AI API Gateway"]
        GoogleDrive["Google Drive API (rclone mount)"]
        GoogleOAuth["Google OAuth 2.0 Identity Providers"]
    end

    %% Network flows and boundaries
    UI -->|"Proxy REST Requests"| CR_FE
    CR_FE -->|"Route to /api/*"| CR_BE
    WS_Client -->|"WebSocket connection (ws://)"| CR_BE
    Audio -->|"PCM bytes (16kHz)"| WS_Client
    
    CR_BE -->|"verify_id_token()"| FB_Auth
    CR_BE -->|"Read/Write state"| FirestoreDB
    CR_BE -->|"Encrypted Credentials"| SecretManager
    CR_BE -->|"gRPC / HTTPS"| VertexAI

    CR_BE -->|"Execute commands / GUI events"| E2B
    E2B -->|"VNC Stream (Frames)"| Desktop
    
    CR_BE -->|"Mount drive & file operations"| GoogleDrive
    CR_BE -->|"Fallback Models / Keys"| KiloGateway
    FB_Client -->|"Authenticate"| GoogleOAuth
```

---

## 📂 2. Backend Directory & Component Mapping

The following diagram maps the logical backend structure located under the [agent/nexus/](file:///c:/Users/deeks/OneDrive/Documents/nexus-agent-main/agent/nexus) directory to their functional responsibilities in Python:

```text
agent/nexus/
├── agents/                       # Google ADK multi-agent configurations
│   ├── __init__.py
│   ├── orchestrator_agent.py     # Root Agent: determines routing & tool delegation
│   ├── sub_agents.py             # Custom Agent types: Computer, Browser, Code
├── prompts/                      # Prompt templates for system guiding and personas
│   ├── __init__.py
│   └── system.py
├── tools/                        # Python functions registered as LLM tools
│   ├── __init__.py
│   ├── bash.py                   # Run shell commands in the E2B sandbox
│   ├── browser.py                # Launch and scrape web browsers
│   ├── computer.py               # Simulate mouse movement, clicks, key presses
│   ├── screen.py                 # Capture screenshot frames of the sandbox desktop
│   └── integrations.py           # Sync operations, e.g. Rclone configurations
├── server.py                     # FastAPI application endpoints & REST handlers
├── ws_handler.py                 # Handles multiplexed real-time WebSocket traffic
├── orchestrator.py               # The main task loop driver (NexusOrchestrator)
├── voice.py                      # Connection manager for Gemini Live API
├── vision.py                     # Image analyzer for parsing screenshots via Gemini
├── sandbox.py                    # Life-cycle manager for E2B Docker containers
├── session.py                    # Active user sessions, metadata, and status tracker
├── auth.py                       # Token parsing, Firebase claim verification
└── crypto.py                     # BYOK (Bring Your Own Key) AES encryption engine
```

---

## 🔄 3. Detailed Processing Loops

### A. Live Audio Loop (Gemini Live)
The system leverages low-latency, bidirectional audio streaming using **Gemini Live 2.5 Flash**. The client streams raw audio inputs, and the backend bridges them to Google's Live API.

```mermaid
sequenceDiagram
    autonumber
    participant Client as 🖥️ Client Web Browser
    participant WS as ⚙️ WebSocket Handler (ws_handler.py)
    participant VoiceMgr as 🎤 Voice Manager (voice.py)
    participant GemLive as 💎 Gemini Live (Google Vertex)
    participant Sandbox as 📦 E2B Sandbox

    Client->>WS: Open WebSocket Connection
    WS-->>Client: Connection Established
    
    Client->>WS: Send Audio Frame (16kHz PCM bytes)
    WS->>VoiceMgr: process_incoming_audio()
    VoiceMgr->>GemLive: Send Real-Time Audio Chunk (WebSocket/gRPC)

    GemLive-->>VoiceMgr: User Speech Transcript Event
    VoiceMgr-->>WS: Forward Transcription
    WS-->>Client: Update UI (User Transcribed Speech)

    alt Model Decides to Execute Tool
        GemLive->>VoiceMgr: ToolCallRequest (e.g. run_command, take_screenshot)
        VoiceMgr->>Sandbox: Execute action in sandbox
        Sandbox-->>VoiceMgr: Tool Execution Result
        VoiceMgr->>GemLive: ToolCallResponse (Result payload)
    end

    GemLive-->>VoiceMgr: Output Audio Event (PCM audio chunk)
    VoiceMgr-->>WS: Forward Audio Frames
    WS-->>Client: Play Audio Stream via Web Audio API
```

---

### B. Agent Reasoning & Tool-Calling Loop (Google ADK)
When the user sends a text command, or when voice instructs the system to perform a complex action, the **Google Agent Development Kit (ADK)** takes over to orchestrate actions recursively.

```mermaid
sequenceDiagram
    autonumber
    participant Orch as 🧠 NexusOrchestrator (orchestrator.py)
    participant ADK as 🤖 ADK Runner (google.adk)
    participant MainAgent as 🎯 Orchestrator Agent (orchestrator_agent.py)
    participant SubAgent as 🖱️ Specialist Agent (Computer/Browser/Code)
    participant Tool as 🔧 Sandbox Tool (tools/*.py)
    participant Sandbox as 📦 E2B Sandbox (sandbox.py)

    Orch->>ADK: run_agent_turn(user_message)
    ADK->>MainAgent: Process request
    
    rect rgb(240, 248, 255)
        Note over MainAgent, SubAgent: Routing & Delegation Phase
        MainAgent->>MainAgent: Analyze task goal
        MainAgent->>SubAgent: Delegate sub-task (e.g. "Install dependencies")
    end

    loop Execution Loop (Max 30 Turns)
        SubAgent->>Tool: Invoke Tool with parameters
        Tool->>Sandbox: Execute OS action (terminal, click, type)
        Sandbox-->>Tool: Return status, file output, or GUI state
        Tool->>Sandbox: Take screenshot (perceive result)
        Sandbox-->>Tool: Screenshot image bytes (JPEG)
        Tool-->>SubAgent: Combined tool result + screenshot
        SubAgent->>SubAgent: Analyze visual/textual feedback
    end

    SubAgent-->>MainAgent: Report sub-task completion status
    MainAgent-->>ADK: Compile final textual report
    ADK-->>Orch: Yield agent response
```

---

## 🔒 4. Authentication, Security, and Encryption (BYOK)

To safeguard user credentials and keep hosting costs transparent, CoComputer features a **Bring Your Own Key (BYOK)** encryption model.

1. **Authentication Flow**:
   - The user signs in on the Next.js Frontend using **Firebase Authentication** (Google OAuth).
   - This yields a Firebase ID Token, which is passed in the headers of all REST requests (`Authorization: Bearer <ID_TOKEN>`).
   - The backend interceptor ([auth.py](file:///c:/Users/deeks/OneDrive/Documents/nexus-agent-main/agent/nexus/auth.py)) verifies the signature using the Firebase Admin SDK and decodes user identifiers (`uid`).

2. **BYOK Encryption Protocol**:
   - When a user enters their personal API keys (Vertex AI, Anthropic, or E2B) in the Settings panel:
     - The FastAPI server generates a session/user-specific salt.
     - The key is encrypted inside [crypto.py](file:///c:/Users/deeks/OneDrive/Documents/nexus-agent-main/agent/nexus/crypto.py) using **AES-GCM-256** encryption.
     - The encrypted ciphertext is stored in the Firestore user profile.
   - When launching an E2B Sandbox or starting an orchestrator turn:
     - The server fetches the ciphertext from Firestore.
     - It decrypts the keys in-memory.
     - The decrypted keys are injected as transient environment variables in the E2B sandbox session or passed directly to the `google.genai.Client`. They are never written to disk or logged.

---

## 🌐 5. Deployment Setup

```mermaid
graph LR
    GitHub["GitHub Repo<br/>(Clean History)"] -->|"CI/CD Trigger"| GCB["Google Cloud Build"]
    GCB -->|"Build & Push Container"| GAR["GCP Artifact Registry"]
    GAR -->|"Deploy Service"| CR_FE["Cloud Run: Frontend"]
    GAR -->|"Deploy Service"| CR_BE["Cloud Run: Agent Backend"]

    style GitHub fill:#fafafa,stroke:#333
    style GCB fill:#e3f2fd,stroke:#1e88e5
    style GAR fill:#e8f5e9,stroke:#43a047
    style CR_FE fill:#ffebee,stroke:#e53935
    style CR_BE fill:#ffebee,stroke:#e53935
```

- **CI/CD Configuration**: Configured under [.github/workflows/google-cloudrun-docker.yml](file:///c:/Users/deeks/OneDrive/Documents/nexus-agent-main/.github/workflows/google-cloudrun-docker.yml). It automates Cloud Run container updates on pushing code commits.
- **Port Mapping**:
  - The Frontend container operates on port `3000`.
  - The Backend container operates on port `8080`.
- **CORS Handling**: Backend specifies CORS origins, which dynamically update to match the frontend Cloud Run URL on deployment.
