# SahayMitra Project Structure

This document details the file and directory layout of the SahayMitra repository, clarifying active modules and planned component stubs.

---

## Annotated Directory Tree

```
SahayMitra/
├── .env.example                     # Template for environment configuration (API keys)
├── .env.local                       # Local environment file (ignored by Git, stores active GEMINI_API_KEY)
├── .gitignore                       # Git ignore list for Node, dependencies, and environment files
├── .github/                         # GitHub repository configuration and issue templates
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md            # Template for reporting bugs
│   │   └── feature_request.md       # Template for proposing new features
│   └── PULL_REQUEST_TEMPLATE.md     # Template for code review checklists
├── CHANGELOG.md                     # Version history and release notes
├── CODE_OF_CONDUCT.md               # Contributor Covenant v2.1 standards
├── CONTRIBUTING.md                  # Development guidelines and pull request instructions
├── LICENSE                          # MIT License file
├── README.md                        # Primary project documentation and quickstart
├── App.tsx                          # [Placeholder] Intended root React component (0 bytes in prototype)
├── index.html                       # Main HTML shell, Tailwind configuration, inline styles, DOM layout
├── index.tsx                        # Core application engine: state, routing, UI renderers, and AI logic
├── metadata.json                    # Application metadata (e.g., requested browser camera permissions)
├── package.json                     # NPM dependencies, scripts, and build metadata
├── tsconfig.json                    # TypeScript compiler options (ES2022, JSX, bundler resolution)
├── types.ts                         # [Placeholder] Type definitions file (0 bytes in prototype)
├── vite.config.ts                   # Vite bundler configuration, dev server port, and env aliases
├── components/                      # Modular UI component directory (reserved for future refactor)
│   ├── Phase1_Onboarding.tsx        # [Placeholder] Planned onboarding phase component
│   ├── Phase2_DataIngestion.tsx     # [Placeholder] Planned data ingestion phase component
│   ├── Phase3_AICore.tsx            # [Placeholder] Planned AI scoring phase component
│   ├── Phase4_Disbursement.tsx      # [Placeholder] Planned loan disbursement phase component
│   ├── Phase5_LoanMonitoring.tsx    # [Placeholder] Planned loan monitoring phase component
│   └── common/                      # [Placeholder] Shared atomic UI components
│       ├── Button.tsx               # [Placeholder] Reusable button component
│       ├── Card.tsx                 # [Placeholder] Reusable card component
│       ├── IconComponents.tsx       # [Placeholder] Reusable icon wrappers
│       ├── LoadingSpinner.tsx       # [Placeholder] Reusable loading spinner
│       └── Stepper.tsx              # [Placeholder] Reusable stepper component
├── docs/                            # In-depth architectural and developer documentation
│   ├── API.md                       # API reference (external Gemini API integration and schemas)
│   ├── ARCHITECTURE.md              # System architecture, Mermaid diagrams, and design decisions
│   ├── PROJECT_STRUCTURE.md         # Detailed repository layout and module responsibilities (this file)
│   └── SETUP.md                     # Step-by-step developer setup and troubleshooting guide
└── services/                        # Service layer directory
    └── geminiService.ts             # [Placeholder] Reserved for decoupled Gemini API calls (0 bytes)
```

---

## File Responsibilities & Implementation Status

### Core Runtime Files

| File | Status | Description |
| :--- | :--- | :--- |
| **`index.html`** | Active | Serves as the single-page application entry point. Includes CDN links for TailwindCSS and Font Awesome, Google Fonts, base CSS animations, and markup templates for all application pages (`#landing-page`, `#apply-loan-page`, `#dashboard-page`, `#ai-scanner-page`, `#communities-page`, `#ekyc-page`), modals, and notification toasts. |
| **`index.tsx`** | Active | The primary runtime engine (~1,300 lines of TypeScript). Initializes the Google Gemini API client, maintains in-memory stores (`beneficiariesData`, `communitiesData`, `pendingApplications`), handles hash-based navigation, binds Web Speech recognition, controls webcam video streams for E-KYC, and handles borrower and lender dashboard interactions. |
| **`vite.config.ts`** | Active | Bundler configuration defining the development server (`port: 3000`, `host: '0.0.0.0'`), setting the `@` directory path alias, and exposing `process.env.API_KEY` and `process.env.GEMINI_API_KEY` to the client bundle via `define`. |
| **`package.json`** | Active | Declares dependencies (`react`, `react-dom`, `@google/genai`) and developer tooling (`vite`, `@vitejs/plugin-react`, `typescript`, `@types/node`). |
| **`metadata.json`** | Active | Used by web containers and preview environments (such as Google AI Studio) to declare frame permissions (specifies `requestFramePermissions: ["camera"]`). |

### Stubs & Future Components

In the initial hackathon sprint, the UI was consolidated inside `index.html` and `index.tsx` to speed up deployment in Google AI Studio. The following files exist on disk as stubs (0 bytes) and are earmarked for future refactoring:

- **`App.tsx` & `types.ts`**: Planned root component and shared interface definitions (`Beneficiary`, `LoanApplication`, `Community`, `ShapFeature`).
- **`services/geminiService.ts`**: Planned abstraction to decouple `@google/genai` calls from DOM manipulation.
- **`components/Phase1_Onboarding.tsx` through `Phase5_LoanMonitoring.tsx`**: Planned multi-phase component decomposition corresponding to the lending lifecycle.
- **`components/common/*`**: Planned atomic React UI primitives (`Button`, `Card`, `Stepper`, `LoadingSpinner`, `IconComponents`).
