# 🤖 Kio Multi-Engine AI CLI Router (`scripts/kio_router.py`)

Multi-Engine Orchestration for the Kio System. Automatically detects installed local AI CLIs, classifies tasks by domain strength, and dispatches headless jobs with fallback protection.

---

## 🎯 Routing Matrix & Engine Strengths

| CLI Engine | Binary | Representative Models | Core Strengths | Assigned Kio Domains |
| :--- | :--- | :--- | :--- | :--- |
| **Claude Code** | `claude` | Claude Sonnet / Opus | • High-craft UI/UX, CSS/Tailwind.<br>• 9-Pass De-slopping (`/unslop`).<br>• Anti-slop high-signal copywriting.<br>• Deep architectural refactoring. | 🎨 **Frontend Craft & Motion**<br>🧹 **UI Refactoring & De-slop**<br>🔍 **Security Code Review**<br>✍️ **High-signal Copywriting** |
| **OpenAI Codex** | `codex` | Codex / GPT-5 / o3 | • Pure algorithmic logic and math.<br>• SQL query tuning (PostgreSQL, Redis).<br>• Comprehensive unit & edge-case tests.<br>• Fast CRUD API & microservice scaffolding. | ⚙️ **Backend Logic & Algorithms**<br>🧪 **Unit & Stress Testing**<br>🗄️ **Database Schema & SQL Tuning**<br>⚡ **Scaffolding Microservices** |
| **Google Antigravity** | `agy` | Gemini Pro / Flash | • Massive context (**1M - 2M tokens**).<br>• Multimodal ingestion (dense PDFs, images, video).<br>• Full-monorepo audits without context overflow.<br>• Google Cloud, Firebase, Android CLI integration. | 📚 **Codebase-wide Audits & Monorepo**<br>📄 **Spec Extraction from PDF / Mockups**<br>📱 **Flutter & Android Native CLI**<br>☁️ **GCP & Firebase Infrastructure** |

---

## 🚀 CLI Usage: `scripts/kio_router.py`

The router script lives at `${KIO_DELIVERY_ROOT:-skills/kio-core-delivery}/scripts/kio_router.py`.

### 1. Inspect available engines on the machine
```bash
python3 "${KIO_DELIVERY_ROOT:-skills/kio-core-delivery}/scripts/kio_router.py" list-engines
# or display the routing matrix:
python3 "${KIO_DELIVERY_ROOT:-skills/kio-core-delivery}/scripts/kio_router.py" matrix
```

### 2. Classify a task prompt
```bash
python3 "${KIO_DELIVERY_ROOT:-skills/kio-core-delivery}/scripts/kio_router.py" classify "Design a Button component with Lenis scroll and Tailwind CSS"
```
*Outputs structured JSON with the recommended engine, confidence score, and classification rationale.*

### 3. Dispatch a task
```bash
# Automatically pick the best available engine and run headless:
python3 "${KIO_DELIVERY_ROOT:-skills/kio-core-delivery}/scripts/kio_router.py" dispatch "Write 10 unit test cases for the discount calculation function"

# Preview the exact CLI command without executing it (Dry-run):
python3 "${KIO_DELIVERY_ROOT:-skills/kio-core-delivery}/scripts/kio_router.py" dispatch "Fix mobile layout overflow" --dry-run

# Force a specific engine:
python3 "${KIO_DELIVERY_ROOT:-skills/kio-core-delivery}/scripts/kio_router.py" dispatch "Refactor auth controller" --engine claude
```

---

## 🛡️ Resilience & Verification Gates

1. **Auto-fallback**: If the primary recommended CLI binary is not installed or fails, the router falls back to the next available engine in the priority chain.
2. **Safety Flags**: Always passes standard headless flags (`--dangerously-skip-permissions` for `claude`/`agy`, `exec` for `codex`) with an explicit execution timeout so background runs never hang.
3. **Verification Gate**: Every change produced by a dispatched sub-CLI must pass `git diff` review and QA tests before merging.
