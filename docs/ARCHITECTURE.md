# SahayMitra Architecture

This document provides a detailed overview of the system architecture, component design, data flow, and technical decisions implemented in the **SahayMitra** prototype.

---

## 1. System Overview

SahayMitra is an AI-powered community lending and microcredit prototype designed for individuals with limited or non-traditional digital footprints. It provides:
1. **Conversational Loan Origination**: Multi-turn dialogue powered by Google Gemini (`gemini-2.5-flash`) with structured schema extraction and browser-native voice I/O.
2. **Explainable Alternative Credit Scoring (XAI)**: SHAP-style positive/negative feature contribution breakdowns.
3. **Simulated Biometric E-KYC**: Step-by-step onboarding leveraging browser webcam access and OTP verification.
4. **Dual-Persona Dashboards**: Tailored views for borrowers ("Living Loan" lifecycle, rewards, statements) and institutional lenders (portfolio KPIs, risk distribution, application approvals).
5. **Community Lending Hub**: Regional self-help lending circles and trust metrics.
6. **AI Paper Record Scanner**: A tool for field lenders to digitize physical ledger documents.

```mermaid
graph TD
    User([User / Borrower or Lender]) -->|Interacts via Browser| Frontend[Single Page Application UI]
    
    subgraph Client Architecture
        Frontend --> Router[Hash-Based SPA Router]
        Router --> Landing[Landing View]
        Router --> Chatbot[AI Loan Application Chatbot]
        Router --> EKYC[Biometric E-KYC Engine]
        Router --> Dashboards[Dual Dashboard View]
        Router --> Communities[Community Lending Hub]
        Router --> Scanner[AI Record Scanner]
        
        Chatbot --> WebSpeech[Web Speech API: STT & TTS]
        EKYC --> WebCam[MediaDevices API: Video Stream]
        Scanner --> FileReader[HTML5 FileReader]
        
        State[(In-Memory Session State: beneficiariesData & pendingApplications)]
        Dashboards <--> State
        Chatbot <--> State
        EKYC <--> State
        Communities <--> State
    end

    subgraph External Cloud Services
        Chatbot -->|Direct SDK Calls / GoogleGenAI| GeminiAPI[Google Gemini 2.5 Flash API]
    end
```

---

## 2. Component Architecture

Although future architectural refactorings have reserved component directories (`components/Phase1_Onboarding.tsx` through `Phase5_LoanMonitoring.tsx`), the current prototype executes as a modular client-side TypeScript application structured around `index.html` and `index.tsx`.

```mermaid
classDiagram
    class ApplicationController {
        +initApp()
        +handleNavigation()
        +navigateTo(pageId: string)
    }

    class AuthenticationModule {
        +currentUser: User | null
        +currentDashboardView: 'borrower' | 'lender'
        +showLoginModal()
        +login(user: User)
        +logout()
    }

    class ChatbotAssistant {
        +chatSession: ApplicationSession
        +isBotThinking: boolean
        +isMuted: boolean
        +handleUserMessage(message: string)
        +resetChat()
        +speak(text: string)
        +recognition: SpeechRecognition
    }

    class EKYCEngine {
        +currentKycStep: number
        +videoStream: MediaStream | null
        +initKycFlow()
        +renderKycStepper()
        +renderKycStepContent()
        +startCamera()
        +stopCamera()
    }

    class DashboardManager {
        +renderDashboard()
        +renderBorrowerDashboard(user: User)
        +renderLenderDashboard()
        +showBeneficiaryProfileModal(beneficiary: User)
        +processApplication(beneficiaryId: number, status: string, amount: number)
    }

    class CommunitiesManager {
        +communitiesData: Community[]
        +renderCommunities()
        +attachCommunityEventListeners()
        +updateJoinedCommunityButtons()
    }

    class RecordScanner {
        +setupAiScannerListeners()
        +scanRecord()
    }

    ApplicationController --> AuthenticationModule
    ApplicationController --> ChatbotAssistant
    ApplicationController --> EKYCEngine
    ApplicationController --> DashboardManager
    ApplicationController --> CommunitiesManager
    ApplicationController --> RecordScanner
```

---

## 3. Data Flow

### 3.1. Conversational Loan Application Flow

```mermaid
sequenceDiagram
    autonumber
    actor Borrower
    participant Chatbot as Chatbot UI (index.tsx)
    participant Speech as Web Speech API
    participant Gemini as Gemini 2.5 Flash API
    participant Store as In-Memory Store

    Borrower->>Chatbot: Types message or speaks via microphone
    opt Voice Input
        Speech-->>Chatbot: Transcribes speech (en-IN)
    end
    Chatbot->>Gemini: models.generateContent(prompt, systemInstruction, responseSchema)
    Note over Gemini: Extracts name, loan purpose, amount, and status
    Gemini-->>Chatbot: Structured JSON response
    Chatbot->>Borrower: Renders response bubble & speaks audio
    alt Application Confirmed
        Chatbot->>Store: Creates application (status: 'Pending Review' or 'Pending KYC')
        Chatbot->>Borrower: Provides User ID & invites to E-KYC / Dashboard
    end
```

### 3.2. Institutional Lender Approval Flow

```mermaid
sequenceDiagram
    autonumber
    actor Lender
    participant Dash as Lender Dashboard
    participant Modal as Beneficiary Profile Modal
    participant Store as In-Memory Store

    Lender->>Dash: Logs in with ID 'lender01'
    Dash->>Store: Reads pendingApplications & beneficiariesData
    Dash->>Lender: Renders KPI summaries, portfolio risk, and pending reviews
    Lender->>Modal: Clicks "Review" or "View Profile"
    Modal->>Lender: Displays composite score (base 600 + SHAP feature values)
    Lender->>Modal: Clicks "Approve Loan" or "Reject Application"
    Modal->>Store: Updates utilized_amount, available_limit, and transaction history
    Modal->>Dash: Re-renders lender portfolio metrics
```

---

## 4. Key Design Decisions

| Decision | Rationale | Trade-offs & Limitations |
| :--- | :--- | :--- |
| **Direct Client SDK Integration** (`@google/genai`) | Eliminates the need for a dedicated backend server during hackathon demonstrations, keeping setup to a single `npm run dev`. | Exposes the API key in client bundles if deployed publicly. Requires migrating to a secure backend proxy before production. |
| **Structured Output Schema** (`responseSchema`) | Constrains Gemini output to predictable JSON fields (`extractedName`, `extractedPurpose`, `extractedAmount`, `isApplicationComplete`), avoiding hallucinated parsing logic. | Model must support structured outputs (`gemini-2.5-flash`). |
| **In-Memory Mock State** | Allows interactive simulation of state transitions (loan repayment, limits, approvals, KYC step progression) without setting up a database. | State resets upon page refresh or process restart. |
| **Hash-Based Client Routing** | Simple, zero-dependency routing (`#landing`, `#apply-loan`, `#dashboard`, etc.) compatible with any static file server or preview environment without server-side rewrite rules. | URL history is simple; deep linking is limited to predefined view identifiers. |
| **Web Speech API Integration** | Lowers literacy barriers for rural/informal sector borrowers by enabling voice-first interaction without requiring heavy third-party audio streaming libraries. | Browser-dependent (supported primarily in Chromium-based browsers; fallback required for Safari/Firefox). |

---

## 5. Implementation Status: Real vs. Mocked

| Feature Area | Sub-Feature | Implementation Status | Implementation Detail |
| :--- | :--- | :--- | :--- |
| **AI Conversational Agent** | Natural Language Extraction | **Real** | Uses live Google Gemini 2.5 Flash API with JSON schema enforcement. |
| **AI Conversational Agent** | Speech-to-Text & Text-to-Speech | **Real** | Uses browser native `SpeechRecognition` and `SpeechSynthesis`. |
| **E-KYC Verification** | Camera feed | **Real** | Connects to hardware camera via `navigator.mediaDevices.getUserMedia`. |
| **E-KYC Verification** | Aadhaar & OTP | **Mocked** | Accepts hardcoded OTP `123456`. |
| **E-KYC Verification** | Facial Liveness Detection | **Mocked** | 2.5-second simulated timeout spinner before approval. |
| **Alternative Credit Scoring** | Score breakdown | **Simulated** | SHAP values calculated from mock feature weights on a 600-point base score. |
| **AI Record Scanner** | File selection & preview | **Real** | HTML5 file reader creates instant image preview. |
| **AI Record Scanner** | OCR Extraction | **Mocked** | Returns static sample CSV table after a 2.5-second simulation timer. |
| **Data Persistence** | User & loan state | **In-Memory** | Stored in JavaScript runtime arrays (`beneficiariesData`, `pendingApplications`). |
