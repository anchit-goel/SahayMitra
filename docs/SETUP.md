# SahayMitra Setup & Troubleshooting Guide

This guide walks you through setting up and running **SahayMitra** on your local machine, along with troubleshooting common runtime errors.

---

## 1. Prerequisites

Before running the application, ensure your development environment satisfies the following requirements:

| Dependency | Minimum Version | Recommended Version | Verification Command |
| :--- | :--- | :--- | :--- |
| **Node.js** | `v18.0.0` or higher | `v20.x` or `v22.x` (LTS) | `node -v` |
| **npm** | `v9.0.0` or higher | `v10.x` | `npm -v` |
| **Modern Browser** | Latest Chromium-based browser (Chrome, Edge, Brave) | Google Chrome (for full Web Speech API support) | — |
| **Gemini API Key** | Google AI Studio account | Active Gemini API key | [Get API Key](https://aistudio.google.com/app/apikey) |

---

## 2. Step-by-Step Installation

### Step 1: Clone the Repository
```bash
git clone https://github.com/your-username/SahayMitra.git <!-- TODO: verify repo URL -->
cd SahayMitra
```

### Step 2: Install Dependencies
Install dependencies declared in [package.json](file:///Users/anchitgoel/Downloads/SahayMitra/package.json):
```bash
npm install
```

### Step 3: Configure Environment Variables
Copy the template environment file to create `.env.local`:
```bash
cp .env.example .env.local
```
Open `.env.local` and replace `your_gemini_api_key_here` with your actual Google Gemini API key:
```env
GEMINI_API_KEY=AIzaSy...
```

---

## 3. Running Locally

Start the Vite development server:
```bash
npm run dev
```

Once running, the terminal will display:
```
  VITE v6.2.0  ready in 250 ms

  ➜  Local:   http://localhost:3000/
  ➜  Network: http://<your-ip>:3000/
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## 4. Test Credentials & Demo Accounts

Because this prototype operates on in-memory mock datasets, pre-configured accounts are available for testing:

### Borrower / Beneficiary Accounts
| User ID | Name | Initial Credit Score | Risk Band | Pre-approved Limit | Utilized Amount |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `sunita101` | Sunita Devi | 785 | Low Risk-High Need | ₹50,000 | ₹0 |
| `ramesh102` | Ramesh Kumar | 650 | High Risk-High Need | ₹25,000 | ₹15,000 |
| `priya103` | Priya Singh | 810 | Low Risk-Low Need | ₹75,000 | ₹0 |

### Lender Account
| User ID | Organization | Available Actions |
| :--- | :--- | :--- |
| `lender01` | NBCFDC Lender | View portfolio KPIs, inspect risk distribution, review new applications, approve/reject loans |

### E-KYC Test Values
| Field | Value to Enter |
| :--- | :--- |
| Aadhaar Number | Any 12 digits (e.g., `1234 5678 9012`) |
| OTP Verification | `123456` (hardcoded check) |
| Facial Verification | Click **"Verify My Identity"** and grant camera permissions |

---

## 5. Troubleshooting & FAQ

### Issue 1: Gemini API Errors or Chatbot Silence
* **Symptoms**: The loan assistant thinking indicator hangs, or the console shows `Gemini API Error: 400/403/404`.
* **Causes**:
  1. `GEMINI_API_KEY` is missing or invalid in `.env.local`.
  2. The Vite dev server was not restarted after editing `.env.local`.
* **Fix**:
  - Verify that `GEMINI_API_KEY` is present in `.env.local`.
  - Restart the Vite dev server (`Ctrl+C` followed by `npm run dev`).
  - Ensure your API key has quota enabled for `gemini-2.5-flash` in Google AI Studio.

### Issue 2: Camera Not Working during E-KYC
* **Symptoms**: The browser displays `Camera access is required for facial verification` or video feed is blank.
* **Causes**:
  1. Browser blocked webcam permissions.
  2. The page is served over an insecure HTTP context on a remote host (browsers require HTTPS or `localhost` for `getUserMedia`).
* **Fix**:
  - Access the application using `http://localhost:3000` rather than an external IP address.
  - In Chrome, click the camera icon on the left side of the address bar and select **"Always allow"**.

### Issue 3: Voice Input / Speech Recognition Not Responding
* **Symptoms**: Clicking the microphone icon produces no transcript or triggers `Microphone access denied`.
* **Causes**:
  1. The browser does not support the Web Speech API (Firefox and Safari do not support `webkitSpeechRecognition` out of the box).
  2. Microphone permission is blocked.
* **Fix**:
  - Use Google Chrome or Microsoft Edge for testing voice input.
  - Allow microphone access in your browser site permissions.

### Issue 4: Port 3000 Already in Use
* **Symptoms**: Vite fails with `Error: listen EADDRINUSE: address already in use 0.0.0.0:3000`.
* **Fix**:
  - Free port 3000:
    ```bash
    # On macOS / Linux
    lsof -i :3000 | awk 'NR>1 {print $2}' | xargs kill -9
    ```
  - Alternatively, specify another port when starting Vite:
    ```bash
    npx vite --port 3001
    ```
