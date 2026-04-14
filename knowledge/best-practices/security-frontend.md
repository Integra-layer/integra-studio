# Frontend Security Best Practices

Security guidelines specifically for frontend code in Integra dApps. For smart contract security, see the review skill and conventions.md.

---

## 1. Never Expose Secrets

- **Never** put private keys, API secrets, or seed phrases in frontend code
- **`NEXT_PUBLIC_`** prefix means the variable is bundled into client-side JavaScript — visible to everyone
- Safe to expose: contract addresses, chain IDs, RPC URLs, Web3Auth client ID
- **Never expose**: API keys with write access, admin private keys, database credentials
- Always provide `.env.example` with placeholder values. Always `.gitignore` the `.env` file

---

## 2. Input Validation

- Validate all user inputs **before** sending to contracts
- **Addresses**: Check `isAddress()` from viem before passing to contract calls
- **Amounts**: Check > 0, check <= user balance, handle decimals correctly (18 for EVM)
- **Strings**: Sanitize to prevent XSS if displayed back in UI
- **Never trust URL parameters** — validate and sanitize query strings

---

## 3. XSS Prevention

- **Never use `dangerouslySetInnerHTML`** — especially with Stitch-generated HTML
- React auto-escapes JSX expressions — don't bypass this
- If you must render HTML (e.g., markdown), use a sanitization library (DOMPurify)
- Sanitize all external content before rendering
- CSP headers: Set `Content-Security-Policy` to restrict script sources

---

## 4. Transaction Safety

- **Always show confirmation dialogs** before signing transactions
- Display: action description, token amount, gas estimate, recipient address
- **Simulate transactions** when possible (use `eth_estimateGas` to catch reverts before signing)
- **Rate-limit actions**: Prevent double-clicks from sending duplicate transactions
- **Check chain ID**: Verify user is on correct network before any transaction
- **Handle nonce conflicts**: If a tx is pending, warn before sending another

---

## 5. Dependency Security

- Audit `package.json` regularly with `npm audit`
- Pin dependency versions (no `^` ranges in production)
- Prefer well-maintained, widely-used packages
- Never install packages from untrusted sources
- Review new dependencies before adding (check GitHub stars, last update, known vulnerabilities)

---

## 6. Authentication

- Use Web3Auth for user authentication — never roll custom auth
- Verify JWT tokens server-side if your dApp has a backend
- Session management: tokens should expire, refresh tokens stored securely
- Never store auth tokens in localStorage (use httpOnly cookies for backend sessions)

---

## 7. Error Handling

- **Never expose stack traces** or internal error details to users
- Log errors to console in development, to a service in production
- Show user-friendly error messages with actionable next steps
- Handle all promise rejections (unhandled rejections crash the app)
- Use React Error Boundaries for graceful component-level failure

---

## 8. Stitch AI Safety

When using Google Stitch for UI generation:
- **Never ship Stitch HTML as production code** — always rebuild as React components
- **Never use `dangerouslySetInnerHTML`** with Stitch output
- **Never copy-paste HTML directly** — extract the design intent, build React components
- Stitch is for **layout inspiration**, not code generation

---

## References

- `knowledge/conventions.md` — TypeScript strict mode and coding standards
- `knowledge/stitch-integration.md` — Stitch safety rules
- `skills/review/SKILL.md` — Security review checklist
