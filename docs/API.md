# SahayMitra API & Integration Reference

This document outlines the API integrations, data schemas, and contracts used in **SahayMitra v0.1.0**.

> [!NOTE]
> **No Backend REST API in v0.1.0**: The current prototype does not host a custom backend server (such as Express, FastAPI, or Django). All application state is managed in-memory on the client, and external AI communication occurs directly between the browser and the Google Gemini API.

---

## 1. External AI Integration: Google Gemini API

The conversational loan onboarding assistant is powered by the `@google/genai` SDK querying the `gemini-2.5-flash` model.

### 1.1. Client Initialization
```typescript
import { GoogleGenAI, Type } from "@google/genai";

const ai = new GoogleGenAI({ apiKey: process.env.API_KEY });
```

### 1.2. Loan Assistant Request Specification

* **Model**: `gemini-2.5-flash`
* **Response Format**: `application/json`
* **Schema Definition**: Strict JSON schema constraint using `Type.OBJECT`

#### Structured Output Schema (`responseSchema`)
```typescript
{
  type: Type.OBJECT,
  properties: {
    responseMessage: {
      type: Type.STRING,
      description: "The text response to display to the user."
    },
    extractedName: {
      type: Type.STRING,
      description: "The user's name you extracted, if any."
    },
    extractedPurpose: {
      type: Type.STRING,
      description: "The loan purpose you extracted, if any."
    },
    extractedAmount: {
      type: Type.NUMBER,
      description: "The loan amount you extracted as a number, if any."
    },
    isApplicationComplete: {
      type: Type.BOOLEAN,
      description: "Set to true ONLY if the user has confirmed the collected details are correct."
    },
    isRestart: {
      type: Type.BOOLEAN,
      description: "Set to true if the user wants to restart the process."
    }
  },
  required: ['responseMessage', 'isApplicationComplete', 'isRestart']
}
```

### 1.3. Sample Request & Response

#### User Input (Turn 1)
```
"Hello, my name is Anita Sharma and I need a loan of 40000 rupees for my tailoring shop."
```

#### Gemini API Structured JSON Response
```json
{
  "responseMessage": "Hello Anita! I would be glad to help you apply for ₹40,000 for your tailoring shop. Before we proceed, could you please confirm if these details are correct?",
  "extractedName": "Anita Sharma",
  "extractedPurpose": "tailoring shop",
  "extractedAmount": 40000,
  "isApplicationComplete": false,
  "isRestart": false
}
```

#### User Input (Turn 2)
```
"Yes, that is correct."
```

#### Gemini API Structured JSON Response
```json
{
  "responseMessage": "Great! One moment while I prepare your application.",
  "extractedName": "Anita Sharma",
  "extractedPurpose": "tailoring shop",
  "extractedAmount": 40000,
  "isApplicationComplete": true,
  "isRestart": false
}
```

---

## 2. In-Memory Data Models & Schemas

### 2.1. Beneficiary Schema
```typescript
interface Beneficiary {
  id: number;                          // Unique identifier (e.g., 101)
  name: string;                        // Full name of beneficiary
  kyc_complete: boolean;               // E-KYC verification flag
  credit_score: number;                // Score (e.g., 300 - 900)
  risk_band: string;                   // E.g. 'Low Risk-High Need', 'Awaiting KYC'
  shap: Array<{                        // Explainable feature attribution
    val: number;                       // Score contribution (+/- integer)
    desc: string;                      // Description of factor
  }>;
  loan_details: {
    approved_limit: number;            // Total sanction limit in INR
    available_limit: number;           // Remaining drawable limit in INR
    utilized_amount: number;           // Currently outstanding principal in INR
    last_payment_date: string;         // 'YYYY-MM-DD' or 'N/A'
    interest_rate: string;             // Monthly interest rate string (e.g., '1.5%')
    next_repayment_date: string;       // Repayment schedule string
    transaction_history: Array<{       // Statement ledger
      date: string;
      desc: string;
      debit: number;
      credit: number;
      balance: number;
    }>;
  };
  achievements: {
    kyc: boolean;
    first_repayment: boolean;
    payment_streak: number;
  };
  impact_tokens: number;               // Social impact loyalty token balance
  loan_application?: LoanApplication;  // Active pending loan request
  joined_communities?: string[];       // Names of joined community circles
}
```

### 2.2. Loan Application Schema
```typescript
interface LoanApplication {
  beneficiaryId: number;
  beneficiaryName: string;
  purpose: string;
  amount: number;
  status: 'Pending KYC' | 'Pending Review' | 'Approved' | 'Rejected';
  progress: number;                    // Completion percentage (0 - 100)
}
```

### 2.3. Community Schema
```typescript
interface Community {
  name: string;                        // E.g. "Green Valley Farmers"
  location: string;                    // City / District, State
  purpose: string;                     // Focus sector (Agriculture, Handicrafts)
  trustScore: number;                  // Community reliability index (0 - 100)
}
```

---

## 3. Proposed Future Backend Endpoints (Target Architecture)

For production deployment, the following RESTful API endpoints are proposed to replace the current client-side state:

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/api/v1/auth/login` | Authenticate user via credentials or OTP |
| `POST` | `/api/v1/kyc/verify-aadhaar` | Initiate Aadhaar verification via UIDAI gateway |
| `POST` | `/api/v1/kyc/verify-face` | Server-side biometric facial matching against ID photo |
| `POST` | `/api/v1/chat/message` | Secure backend proxy to Gemini API with session context |
| `GET` | `/api/v1/beneficiaries/:id` | Fetch beneficiary profile, limits, and SHAP breakdown |
| `POST` | `/api/v1/loans/apply` | Submit loan application for underwriting |
| `POST` | `/api/v1/loans/:id/repay` | Process repayment and calculate impact token rewards |
| `POST` | `/api/v1/lender/loans/:id/action` | Lender decision endpoint (approve / reject) |
| `POST` | `/api/v1/ocr/scan-ledger` | Cloud OCR extraction for physical record photos |
