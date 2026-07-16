# AgentCon 2026: Project Submission Specification

* **Project Title:** CoComputer: Multi-Agent Autonomous Cloud Desktop Orchestrator
* **Selected Theme:** Building Enterprise AI Agents, ML Systems & Workflow Automation for Bharat
* **Problem Statement Category:** Enterprise Digital Workforce / Autonomous IT Operations Agent
* **GitHub Repository:** https://github.com/DiganthGowdaGR/nexus-agent.git
* **Demo Video Link:** https://youtu.be/yYetL4iX8Vs (4-minute walk-through of the agent's live browser control, voice, and terminal tasks)
* **Architecture Diagram Path:** `docs/cocomputer_architecture_diagram.png` (Included in repository and documentation)

---

## 🚀 Participant Declaration & Compliance

By submitting this project, the team confirms that:
1. **Original Work:** Developed specifically during the AgentCon 2026 hackathon, without cloning existing products or using pre-existing codebases.
2. **AI Usage Policy:** AI assistants (Gemini, Claude, Cursor) were used strictly to aid development and pair-programming; all code architecture, workflows, and logical implementation are fully understood by the participants.
3. **Open Access:** The repository is public, and the demo video link contains no viewing restrictions for the judges.

---

## 📋 Section 1: Project Metadata

### Project Title
`CoComputer: Multi-Agent Autonomous Cloud Desktop Orchestrator`

### Selected Challenge Area
`Enterprise Digital Workforce`

### Project Short Description
CoComputer is a state-of-the-art autonomous agent designed to navigate, control, and execute complex workflows within secure, persistent cloud-based Linux desktop environments. Powered by Google Gemini models via Vertex AI and built using the Google ADK (Agent Development Kit), it translates voice or text instructions into real-world GUI mouse clicks, keyboard operations, and terminal bash commands. The platform features low-latency bidirectional voice chats (using Gemini Live), secure session persistence, native Google Drive OAuth integration, and end-to-end encryption for user-provided API credentials.

---

## 🔍 Section 2: Detailed Solution Overview

### 1. What problem does your solution solve?
* **Manual Operational Overhead:** Enterprise workers, researchers, and SMEs spend millions of hours executing repetitive, manual digital workflows (e.g., data scraping, form-filling, system syncs, report generation).
* **Fragility of Existing Automation:** Traditional Robotic Process Automation (RPA) tools rely on hardcoded HTML paths or CSS selectors. They break the moment a target website updates its frontend.
* **Security & Device Interruption:** Setting up automation scripts locally locks up the user’s computer (preventing multitasking) and risks exposing local user environments to security threats.
* **Impact for Bharat:** CoComputer provides an offloaded, secure "digital worker" executing tasks in the cloud. This frees up local computing resources and allows users (even non-technical ones) to delegate office work securely using plain language or speech.

---

### 2. How does your AI Agent work?
CoComputer coordinates multiple AI agents in a continuous loop to perceive the screen, think, and execute actions:

1. **User Input:** The user provides a command via text or real-time voice stream (using the microphone input).
2. **Multi-Agent Coordination (Google ADK):** A master **Orchestrator Agent** analyzes the task requirements and delegates execution steps to specialized sub-agents:
   - **Browser Agent:** Launches Chrome, searches the web, and reads content.
   - **Code Agent:** Installs packages, writes Python scripts, and runs terminal commands.
   - **Computer Agent:** Manages mouse movement, scrolling, typing, and drag-and-drop operations.
3. **The Vision-Action Loop:**
   - The agent takes a screenshot of the remote Linux desktop.
   - It sends the image to **Gemini Vision** to parse visual elements and locate buttons/inputs.
   - It calculates coordinates, executes the next GUI click or keystroke, and verifies the screen changes.
4. **Output & Streaming:** All operations happen safely in a persistent cloud sandbox, with the visual frame feed streamed live to the user interface via an embedded VNC viewer.

---

### 3. What technologies were used?
* **Core Machine Learning & AI:**
  - **Google Gemini 3.0 & 3.1 Pro** for complex vision perception, multi-step planning, and decision-making.
  - **Gemini Live 2.5 Flash** for high-speed, bidirectional real-time audio interaction and transcription.
* **Agent Framework:**
  - **Google Agent Development Kit (ADK)** for agent hierarchy and routing logic.
* **Backend Stack:**
  - **Python & FastAPI** for handling REST APIs and WebSocket multiplexing.
  - **E2B Desktop Sandbox** to spin up isolated, persistent Ubuntu Linux VM container instances.
* **Frontend Web App:**
  - **Next.js (React 19 & TypeScript)**, Tailwind CSS v4, Framer Motion, and noVNC visual player.
* **Cloud Infrastructure & Data Storage:**
  - **Google Cloud Run** for serverless container deployment.
  - **Firebase Authentication** for user login verification.
  - **Google Cloud Firestore** to track sessions, configurations, and user setting histories.
  - **Google Secret Manager** for managing API keys and encryption variables.
* **Integrations:**
  - **Google Drive API & Rclone** for mounting and syncing files dynamically into the sandbox environment.

---

### 4. What makes your solution unique?
* **Screenshot-Based Vision Reasoning:** Instead of reading fragile HTML source code, CoComputer navigates by "looking" at screenshots like a human worker, making the agent robust to layout changes.
* **Persistent Sessions:** Users can start a task, disconnect their browser, and return later to find their background task completed and the VM state saved.
* **Bidirectional Voice Controls:** Allows hands-free operation and conversational workspace control using Gemini Live audio streaming.
* **Dynamic Google Drive Mounts:** Securely links user folders from Google Drive into the sandboxed VM container, facilitating immediate document creation and editing.
* **Privacy-First BYOK Security:** Embeds a "Bring Your Own Key" system where personal API keys are encrypted locally using AES-GCM-256 before upload. Decryption keys are loaded strictly in-memory during active sessions.
