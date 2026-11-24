
# 🚀 Kio Project Initializer & Standardizer

This guide tells the agent how to create, configure and start a new project to Kio's architecture standard when the user says *"create a new project"*, *"create project <name>"*, *"setup project"* or uses `/create-project`.

---

## 🎯 Dynamic Workspace Resolution

**NEVER** hardcode one machine's path. Use the `KIO_WORKSPACE_ROOT` environment variable, defaulting to `~/Projects`:

```bash
# 1. Use KIO_WORKSPACE_ROOT when the user set it; otherwise ~/Projects.
#    Never append the user name to $HOME ($HOME already is /home/<user> or /Users/<user>).
WORKSPACE_ROOT="${KIO_WORKSPACE_ROOT:-$HOME/Projects}"
mkdir -p "$WORKSPACE_ROOT"

# 2. Target project path
TARGET_DIR="$WORKSPACE_ROOT/<project-name>"
```

- The `<project-name>` folder must be lowercase, without accents, using hyphens (kebab-case). Examples: `achiever-art`, `nail-spa-supply`, `creative-lab`.

---

## 📋 Project Initialisation Procedure (5 Steps)

### 1. Analyse the Request & Scan Ports
- **Project name (`PROJECT_ID`)**: take it from the user's request, or ask if unclear.
- **Port (`PORT`)**:
  - If the user specifies one (e.g. `8080`, `8081`, `866`), use it.
  - Otherwise scan for a free port in the `8080` – `8999` range:
    ```bash
    for p in $(seq 8080 8999); do
      if ! ss -tuln | grep -q ":$p "; then
        PORT=$p
        break
      fi
    done
    ```
- **Database name (`DB_NAME`)**:
  - Must follow the project name, converted from kebab-case to snake_case:
    `DB_NAME=$(echo "$PROJECT_ID" | tr '-' '_')` (e.g. `nail-spa` -> `nail_spa`).
- **Admin account (`WP_ADMIN_USER`)**:
  - **NEVER** use `admin`.
  - Standard format: `<project_slug>_admin` or a short form (e.g. `nailspa_admin`).
  - Password: generate a strong random one with `openssl rand -base64 16` or another secure random string.
- **Technology type**:
  - **WordPress FSE (Docker)**: news sites, landing pages, shops, WordPress FSE e-commerce.
  - **Node.js / React / Vite**: single-page frontend apps (SPA).
  - **Next.js App Router**: fullstack SSR / React Server Components.
  - **Python / FastAPI / Flask**: microservices, AI logic, automation backends.

---

### 2. Create the Project Folder
```bash
WORKSPACE_ROOT="${KIO_WORKSPACE_ROOT:-$HOME/Projects}"
mkdir -p "$WORKSPACE_ROOT/<project-name>"
cd "$WORKSPACE_ROOT/<project-name>"
```

---

### 3. Configure by Project Type

#### 🅰️ WordPress FSE Project (Docker Compose)
1. Create `docker-compose.yml` attached to the shared Docker network `webnet` and the shared MySQL service `mysql`:
   ```yaml
   services:
     wordpress:
       image: wordpress:latest
       container_name: ${PROJECT_ID}
       restart: always
       ports:
         - "${PORT}:80"
       environment:
         WORDPRESS_DB_HOST: ${DB_HOST}
         WORDPRESS_DB_USER: ${DB_USER}
         WORDPRESS_DB_PASSWORD: ${DB_PASSWORD}
         WORDPRESS_DB_NAME: ${DB_NAME}
       volumes:
         - ./wp-content:/var/www/html/wp-content
       networks:
         - webnet

   networks:
     webnet:
       external: true
   ```
2. Create `.env` with every environment variable (the database connects 100% through `.env`):
   ```env
   PROJECT_ID=<project-name>
   PORT=<port>
   PROJECT_HOST=http://localhost:<port>
   DB_NAME=<project_slug>
   DB_USER=root
   DB_PASSWORD=root
   DB_HOST=mysql
   WP_ADMIN_USER=<project_slug>_admin
   WP_ADMIN_PASSWORD=<random_password>
   WP_ADMIN_EMAIL=admin@example.com
   ```
3. **Adaptive WordPress theme creation**:
   - **Case 1 (parent theme `ai-zippy` exists in `wp-content/themes/ai-zippy`)**:
     - Create the child theme `ai-zippy-child` in `wp-content/themes/ai-zippy-child/` (`Template: ai-zippy` in `style.css`).
   - **Case 2 (new machine WITHOUT `ai-zippy` — standalone FSE theme fallback)**:
     - Create a **standalone FSE block theme** in `wp-content/themes/<project-slug>/` (**never** write `Template: ai-zippy` in `style.css`, or WordPress reports a missing parent theme):
       - `style.css`: declare `Theme Name: <Project Name>`, `Text Domain: <project-slug>`.
       - `theme.json`: `"version": 3` with `settings.color.palette`, `settings.typography`, `settings.layout` (`contentSize`, `wideSize`).
       - `functions.php`: auto-discover and register every Gutenberg SSR block (`glob(__DIR__ . '/assets/blocks/*/block.json')` ➔ `register_block_type()`), enqueue CSS/JS with `'strategy' => 'defer'`, and include an AJAX form handler with 4 layers of anti-bot spam protection.
       - `parts/header.html` & `parts/footer.html`: FSE template parts editable in the Site Editor.
       - `templates/index.html`, `templates/front-page.html`, `templates/page.html`, `templates/single.html`.
       - SCSS & build structure: `src/scss/layouts/_common-sections.scss`, `src/entry/template/` plus `package.json` (`@wordpress/scripts` / `vite`).
   - **Docker note**: if the machine has no shared `webnet` network and `mysql` container, run `docker network create webnet 2>/dev/null || true` or add a `db` service (`mariadb:10.11`) to `docker-compose.yml` so the project runs fully standalone.
4. Start the containers:
   ```bash
   docker-compose up -d
   ```
5. Install WordPress and create the admin with WP-CLI:
   ```bash
   docker exec -i <container_name> wp core install \
     --url="http://localhost:<port>" \
     --title="<project-name>" \
     --admin_user="<project_slug>_admin" \
     --admin_password="<random_password>" \
     --admin_email="admin@example.com" \
     --allow-root
   ```
6. Update siteurl & home:
   ```bash
   docker exec -i <container_name> wp option update siteurl "http://localhost:<port>" --allow-root
   docker exec -i <container_name> wp option update home "http://localhost:<port>" --allow-root
   ```

#### 🅱️ React / Vite Project (TypeScript)
```bash
WORKSPACE_ROOT="${KIO_WORKSPACE_ROOT:-$HOME/Projects}"
mkdir -p "$WORKSPACE_ROOT" && cd "$WORKSPACE_ROOT"
npx -y create-vite@latest <project-name> --template react-ts
cd <project-name>
npm install
```

#### 🅲 Next.js App Router Project
```bash
WORKSPACE_ROOT="${KIO_WORKSPACE_ROOT:-$HOME/Projects}"
mkdir -p "$WORKSPACE_ROOT" && cd "$WORKSPACE_ROOT"
npx -y create-next-app@latest <project-name> --typescript --tailwind --eslint --app --src-dir --import-alias "@/*" --use-npm --no-git
cd <project-name>
```

#### 🅳 Python / FastAPI Project
```bash
WORKSPACE_ROOT="${KIO_WORKSPACE_ROOT:-$HOME/Projects}"
mkdir -p "$WORKSPACE_ROOT/<project-name>" && cd "$WORKSPACE_ROOT/<project-name>"
python3 -m venv venv
source venv/bin/activate
pip install fastapi uvicorn pydantic python-dotenv
cat << 'EOF' > main.py
from fastapi import FastAPI

app = FastAPI(title="<project-name>")

@app.get("/")
def read_root():
    return {"status": "ok", "project": "<project-name>"}
EOF
```

---

### 4. Mandatory Delivery Report (`src/report-<project-name>.html`)
Every created/handed-over project must include a visual delivery report at `src/report-<project-name>.html` with:
- Admin login details, access URL and port.
- A changelog table of everything that was set up.
- A before vs after comparison table.
- Real test screenshots from Playwright MCP (never fabricated images).

---

### 5. Test & Verify the Port
- Use `curl -I http://localhost:<port>` or Playwright MCP to check for HTTP 200 OK.
- Confirm the container or dev server process listens on the expected port.
