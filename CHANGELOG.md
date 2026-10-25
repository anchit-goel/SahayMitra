# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [0.1.0] - 2025-10-03 <!-- TODO: verify initial SIH release date -->

### Added
- **AI Loan Assistant**: Conversational loan application flow powered by Google Gemini (`gemini-2.5-flash`) via `@google/genai` with structured output schema enforcement.
- **Voice I/O**: Voice input using the browser Web Speech API (`SpeechRecognition`) and audio responses using `SpeechSynthesis`.
- **Explainable Alternative Credit Scoring**: SHAP feature attribution display combining repayment consistency, utility metrics, and community trust.
- **Biometric E-KYC Simulation**: Interactive 4-step wizard with real webcam video stream capture and simulated OTP verification.
- **Dual Dashboard System**:
  - **Borrower View**: Living loan tracker, repayment simulation, statements, and Social Impact Rewards token balance.
  - **Lender View**: Portfolio analytics (Total Beneficiaries, Total Utilized, Average Credit Score), risk distribution breakdown, and loan application review/approval actions.
- **Community Lending Circles**: Directory of regional self-help groups with trust scores and join interactions.
- **AI Record Scanner**: Photo previewer for field lenders to digitize physical non-digital paper ledgers (mock OCR output).
- **Project Documentation**: Initial open-source documentation suite including Architecture, Setup, API reference, Contributing guidelines, and Code of Conduct.

---

## [Unreleased] - Proposed Future Scope
- Migration of `index.tsx` logic into modular React components (`components/Phase1_Onboarding.tsx` through `Phase5_LoanMonitoring.tsx`).
- Integration of actual Cloud OCR engine (e.g., Google Cloud Vision or Document AI) for the AI Record Scanner.
- Persistent database backend (PostgreSQL / MongoDB) with secured REST or GraphQL endpoints.
- Real UIDAI Aadhaar E-KYC integration via licensed e-Sign / e-KYC service providers.
