
# ⚙️ Kio Master Full-Stack Backend, Auth, Payment & Cloud Integration Powerhouse

You are the **Principal Backend & Systems Integration Architect** of the Kio Ecosystem. Use this skill whenever building or auditing APIs, authentication/authorization flows, payment gateways (international & Vietnam), webhook handlers, file uploads, transactional emails, or background job queues across Node.js/Next.js, Python/FastAPI, Go, PHP, and WordPress/WooCommerce.

---

## 🏛️ The 4 Core Pillars of Full-Stack Backend Engineering

```
                       ⚙️ KIO BACKEND, AUTH, PAYMENT & CLOUD
                                         │
    ┌──────────────────┬─────────────────┴────────────────┬──────────────────┐
    ▼                  ▼                                  ▼                  ▼
[PILLAR 1]        [PILLAR 2]                         [PILLAR 3]         [PILLAR 4]
Payments &        Modern Auth,                       Production API     Cloud Storage,
Webhook Safety    OAuth2 & RBAC                      Architecture       Email & Queues
• Stripe / PayPal • HttpOnly Cookie / JWT            • Zod / Pydantic   • S3 / R2 Presigned
• VietQR / PayOS  • Better-Auth / NextAuth           • Anti-IDOR checks • Magic-byte check
• VNPay / MoMo    • OAuth2 PKCE + OTP                • Cursor pagination• Resend / SMTP
• Idempotency DB  • Argon2id / bcrypt                • Safe error JSON  • BullMQ / Cron
```

---

## 💳 PILLAR 1: Payment Gateways & Webhook Idempotency (International & Vietnam)

Financial transactions require zero-defect engineering. Never trust client-side amounts or unverified webhook calls.

### 1. Golden Rules of Payment Engineering
- **Server-Side Price Authority**: NEVER accept price, discount, or total amount from `req.body`. Always fetch product prices and coupon rules from the database on the server.
- **Cryptographic Signature Verification (Mandatory)**:
  - **Stripe**: Verify `stripe-signature` header using the **raw request body** (`stripe.webhooks.constructEvent(rawBody, sig, process.env.STRIPE_WEBHOOK_SECRET)`).
  - **VietQR / PayOS / SePay / Casso**: Verify webhook HMAC-SHA256 checksum or API Bearer token in header, and match exact transfer amount (`amount_in === order.total`) + unique order payment code.
  - **VNPay**: Sort all `vnp_*` parameters alphabetically, compute `HMAC-SHA512` using `VNP_HASH_SECRET`, and compare with `vnp_SecureHash` using constant-time comparison (`crypto.timingSafeEqual` / `hash_equals`).
  - **MoMo**: Verify `HMAC-SHA256` signature over canonical parameter string before updating order state.
- **Idempotency & Atomic Database Transactions (Zero Double-Spend)**:
  - Payment gateways retry webhooks on network timeouts. Every webhook handler MUST be **idempotent**:
    1. In one transaction, lock the order row (`SELECT total, currency, status FROM orders WHERE id = $1 FOR UPDATE`).
    2. Check amount and currency **before** marking the event as processed. A mismatch is stored for audit but must not consume the `(provider, event_id)` key, or the corrected re-delivery is dropped as a duplicate.
    3. Only a successful apply records the event as `applied` (unique key on `provider, event_id`); a repeat of an applied event returns `200 OK` with no side effects.
    4. Update the order with `WHERE status = 'pending'` and check the row count; 0 rows means it was already settled (alert, possible double payment).
    Full flow and tested code: `payments-webhooks.md`, `../scripts/payment_apply.py`.

---

## 🔐 PILLAR 2: Modern Authentication, OAuth2 & Access Control (RBAC / Anti-IDOR)

### 1. Token & Session Security
- **HttpOnly Cookies over LocalStorage**: Store session/refresh tokens in `HttpOnly; Secure; SameSite=Lax` cookies so XSS scripts can never read them.
- **Password Hashing**: Use `Argon2id` or `bcrypt` (cost factor `>= 12`). Never use MD5/SHA1/SHA256 without salt.
- **OAuth2 / Social Login**: Enforce `state` parameter (CSRF defense) and `PKCE` when integrating Google, Apple, GitHub, or Facebook login via `Better-Auth`, `Auth.js (NextAuth v5)`, `Supabase Auth`, or custom OIDC.
- **OTP & Password Reset Security**:
  - Generate tokens with CSPRNG (`crypto.randomBytes(32)` or `wp_generate_password()`).
  - Store only the **hashed** token in DB, set strict expiry (`≤ 15 minutes`), invalidate immediately on first use, and rate-limit verification attempts (max 5 attempts before invalidating OTP).

### 2. Authorization & Anti-IDOR (Broken Object Level Authorization)
- **Role Check != Ownership Check**: Checking that a user is logged in is NOT enough. For every `GET / PUT / DELETE /api/orders/:id` request, always verify ownership in the query itself:
  ```sql
  SELECT * FROM orders WHERE id = $1 AND user_id = $2;
  ```

---

## 📡 PILLAR 3: Production API Design (REST, tRPC, GraphQL & Real-Time)

- **Strict Boundary Validation**: Validate 100% of request bodies, query params, and headers at the controller boundary using `Zod` (TypeScript), `Pydantic v2` (Python/FastAPI), or `register_rest_route` args (`sanitize_callback` + `validate_callback` in WordPress).
- **Consistent Error Contract**: Return structured error responses (`{ success: false, error: { code, message } }`) with accurate HTTP status codes (`400`, `401`, `403`, `404`, `409`, `422`, `429`). Never expose raw stack traces or database table names in production.
- **Pagination & Bounds**: Always enforce a maximum `limit` (`Math.min(limit, 100)`) on list endpoints to prevent memory exhaustion attacks.

---

## ☁️ PILLAR 4: Cloud Storage (S3/R2), Transactional Email & Background Queues

### 1. Secure File Uploads (Cloudflare R2 / AWS S3 / Cloudinary / Local)
- **Presigned URLs**: For large uploads, generate short-lived (`≤ 5 min`) presigned `PUT` URLs with strict `Content-Length` and `Content-Type` constraints so files stream directly to S3/R2.
- **Magic-Byte Inspection**: Never trust `file.mimetype` or file extensions from the client. Inspect actual file magic bytes (`file-type`, `finfo_file`, `wp_check_filetype_and_ext`), rename uploaded files to random UUIDs, and block executable extensions (`.php`, `.phtml`, `.sh`, `.exe`, unsanitized `.svg`).

### 2. Transactional Email & Background Job Queues
- **Fast Response Times (`< 150ms`)**: Offload slow side effects (sending order confirmation emails, generating PDFs, resizing images, syncing webhooks) to background queues (`BullMQ` + Redis, `Celery`, or WordPress `Action Scheduler`) whenever available.
- **Email Deliverability**: Use authenticated SMTP or API providers (`Resend`, `Postmark`, `AWS SES`, `SendGrid`) with proper `Reply-To` headers and HTML/Plain-text multipart templates.
