# AgentCon 2026: Official Project Submission Form Details

This document contains the exact entries to be filled out on the AgentCon 2026 Final Project Submission Form.

---

## 👥 Section 1: Team Information

* **Team Name:** `GenSonic`
* **Team Leader Name:** `SHARATH GOWDA GR`
* **Team Leader Email:** `sharathgowdagr08@gmail.com`
* **Team Leader Mobile Number:** `6362472664`

---

## 📋 Section 2: Project Information

* **Project Title:** `CoComputer: Multi-Agent Autonomous Cloud Desktop Orchestrator`
* **Problem Statement Selected:** `Enterprise Digital Workforce`
* **Project Description (500 Words or Fewer):**
  CoComputer is a state-of-the-art autonomous digital worker designed to navigate, control, and execute complex workflows within secure, persistent cloud-based Linux desktop environments. Powered by Google Gemini models via Vertex AI and built using the Google Agent Development Kit (ADK), it translates natural language voice or text instructions into real-world GUI mouse clicks, keyboard operations, and terminal bash commands.

  The platform coordinates a multi-agent system where a master Orchestrator Agent analyzes tasks and delegates sub-tasks to specialist sub-agents (Browser, Code, and Computer Agents) in a continuous "see-think-act" loop. It visually perceives changes on the desktop screen using real-time screenshots and Gemini Vision rather than relying on fragile HTML/CSS selectors, making it highly resilient to website updates.
  
  For enterprise scalability and user convenience, CoComputer supports low-latency bidirectional voice interactions via Gemini Live, complete session persistence where workspaces can be paused and resumed later, secure Google Drive OAuth syncing using rclone mounts, and a privacy-first "Bring Your Own Key" (BYOK) credential vault encrypted locally using AES-GCM-256.

---

## 🔍 Section 3: Solution Overview

### What problem does your solution solve?
* **Manual Operational Bottlenecks:** Knowledge workers, SMEs, and back-offices waste millions of hours on manual, repetitive web and desktop workflows (data scraping, file migrations, system updates).
* **Fragile Traditional RPA:** Legacy Robotic Process Automation (RPA) tools rely on static HTML/CSS select elements. If a website changes its code structure, the automation immediately breaks.
* **Infrastructure Lockup:** Running scripts locally slows down computers, occupies local input devices, and creates local security vulnerabilities.
* **Societal & Business Impact in Bharat:** CoComputer provides an offloaded, secure "digital coworker" running remotely in the cloud. It frees local computing capacity and enables anyone to automate office tasks using natural speech.

### How does your AI Agent work?
* **Inputs:** Accepts plain language instructions via text or bidirectional microphone voice stream.
* **Planning & Multi-Agent Collaboration:** A master Orchestrator Agent parses the goal and delegates tasks to specialized sub-agents:
  * **Browser Agent** for web searching, navigation, and web content scraping.
  * **Code Agent** for terminal operations, package builds, and running code.
  * **Computer Agent** for mouse movement, key entries, and drag-and-drop actions.
* **Vision-Action Decision Process:** The agent takes screenshots of the E2B remote Linux sandbox, reasons on the visual layout using Gemini Vision, clicks coordinates, types text, and loops until the target outcome is achieved.
* **Outputs:** Streams the live remote desktop visually via a web-based VNC viewer and pushes final files directly to the user's mounted Google Drive.

### What technologies were used?
* **Models:** Google Gemini 3.0 & 3.1 Pro (Vision Reasoning), Gemini Live 2.5 Flash (Bidirectional Voice).
* **Agent Framework:** Google Agent Development Kit (ADK) for hierarchical multi-agent coordination.
* **Backend:** Python, FastAPI, and E2B Desktop Sandbox (Ubuntu VM containers).
* **Frontend:** Next.js (React 19, TypeScript, Tailwind CSS v4, Framer Motion, and noVNC player).
* **Cloud & DB:** Google Cloud Run (Serverless hosting), Firebase Authentication, Cloud Firestore (Metadata/History), and Google Secret Manager.
* **Integrations:** Google Drive API & Rclone for secure, dynamic workspace syncing.

### What makes your solution unique?
* **Vision-First Automation:** Operates by visually recognizing elements on the screen, making it immune to code-level layout updates.
* **Stateful VM Session Persistence:** Sessions are persistent, enabling long-running background tasks that survive browser disconnects.
* **Hands-Free Bidirectional Voice UX:** Integrates Gemini Live directly into the workspace for conversational desktop commands.
* **Google Drive Syncing:** Mounts the user's corporate/personal drive securely to read and write document files.
* **End-to-End Encryption (BYOK):** Restricts API key handling strictly in-memory using local AES-GCM-256 decryption.

---

## 🛠️ Section 4: Technical Submission

* **GitHub Repository Link:** `https://github.com/DiganthGowdaGR/nexus-agent.git`
* **Live Demo Link:** `Optional / Not Available`
* **Deployed Application Link:** `Optional / Not Available`
* **Architecture Diagram Link:** `https://drive.google.com/file/d/1k-XYVvw_r8GFV3u0aPYUG7dCRm75wJI6/view?usp=drive_link`

---

## 📁 Section 5: Files Upload

* **PPT / PDF Presentation Link:** `https://drive.google.com/file/d/1l-MCSIqoi9KYjhkTcIPTGkZJo6TFkAIK/view?usp=sharing` (Upload this PDF/PPT file to the form input)
* **Demo Video Link:** `https://drive.google.com/file/d/10OmKc3QjAvpJ9E8gPc6_5KFdXsxkGJwQ/view?usp=drive_link` (Upload this video file to the form input)

---

## 🤖 Section 6: AI & Development Declaration

* **AI Tools Used:**
  - [x] ChatGPT
  - [x] Claude
  - [x] Gemini
  - [x] GitHub Copilot
  - [x] Cursor
  - [ ] Windsurf
  - [ ] LangChain
  - [ ] CrewAI
  - [ ] AutoGen
* **Approximate Human Contribution:**
  - [x] Below 50%

---

## 🚀 Section 7: Final Declaration

By checking the options on the submission form, the team agrees to the following declarations:
* [x] **Project Development:** We confirm that this project was developed during AgentCon 2026.
* [x] **Code Understanding:** We understand and can explain all submitted code.
* [x] **Technical Verification:** We agree to participate in technical questioning and project verification.
* [x] **Intellectual Property:** We confirm that our submission does not violate any intellectual property rights.
* [x] **Truthfulness:** We understand that false information may result in disqualification.
