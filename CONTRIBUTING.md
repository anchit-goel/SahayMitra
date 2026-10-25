# Contributing to SahayMitra

Thank you for your interest in contributing to **SahayMitra**! This project was originated as a Smart India Hackathon (SIH) 2025 prototype aiming to expand credit inclusion for underserved communities. We welcome contributions from developers, designers, and domain experts.

---

## Code of Conduct

All contributors and participants are expected to follow our [Code of Conduct](CODE_OF_CONDUCT.md). Please read it to ensure a respectful and welcoming environment.

---

## Getting Started

1. **Fork the Repository**: Click "Fork" on GitHub and clone your fork locally:
   ```bash
   git clone https://github.com/<your-username>/SahayMitra.git
   cd SahayMitra
   ```
2. **Install Dependencies**:
   ```bash
   npm install
   ```
3. **Configure Environment**:
   ```bash
   cp .env.example .env.local
   # Add your GEMINI_API_KEY to .env.local
   ```
4. **Run the App**:
   ```bash
   npm run dev
   ```

---

## Branch Naming Convention

Create a feature branch with a descriptive name prefixed by the type of change:

| Prefix | Description | Example |
| :--- | :--- | :--- |
| `feat/` | A new feature or major enhancement | `feat/extract-ocr-service` |
| `fix/` | A bug fix | `fix/chat-reconnection-timeout` |
| `docs/` | Documentation additions or edits | `docs/update-architecture-diagram` |
| `refactor/` | Code refactoring without changing behavior | `refactor/migrate-to-react-components` |
| `chore/` | Tooling, configuration, or dependency updates | `chore/update-vite-config` |

```bash
git checkout -b feat/your-feature-name
```

---

## Commit Message Convention

We follow [Conventional Commits](https://www.conventionalcommits.org/):

* `feat: add camera fallbacks for unsupported browsers`
* `fix: correct negative credit calculation in statement ledger`
* `docs: update setup instructions for port overrides`
* `refactor: split index.tsx into modular Phase components`

---

## Pull Request Checklist

Before opening a Pull Request, please ensure:

- [ ] The application compiles cleanly without syntax errors:
  ```bash
  npm run build
  ```
- [ ] No real API keys, credentials, or personal secrets are committed.
- [ ] Environment variable changes are reflected in `.env.example`.
- [ ] If changing UI behavior, provide screenshots or screen recordings in the PR description.
- [ ] Code follows existing conventions and preserves documentation comments.

---

## Code Style & Guidelines

- **TypeScript**: Use TypeScript for all new code. Avoid using `any` wherever possible.
- **Components**: For future component migrations, place components in `components/` and import shared types from `types.ts`.
- **Styling**: Use utility classes adhering to the custom Tailwind configuration in `index.html` (e.g., `brand-blue`, `solid-card`, `primary-btn`).
- **Accessibility**: Ensure keyboard navigability and accessible ARIA attributes for modal dialogs and inputs.
