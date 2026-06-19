# Zyphron Free Dash — Full Documentation

> Complete technical documentation for the Zyphron Free Dash project.  
> Stack: NestJS 11 + Vue 3 + TypeORM + SQLite/MySQL + Pterodactyl Panel

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Tech Stack](#2-tech-stack)
3. [Project Structure](#3-project-structure)
4. [Getting Started](#4-getting-started)
5. [Environment Variables](#5-environment-variables)
6. [Database Schema](#6-database-schema)
7. [Authentication System](#7-authentication-system)
8. [API Endpoints](#8-api-endpoints)
9. [Backend Architecture](#9-backend-architecture)
10. [Frontend Architecture](#10-frontend-architecture)
11. [Key Features](#11-key-features)
12. [External Integrations](#12-external-integrations)
13. [Cron Jobs](#13-cron-jobs)
14. [Admin Panel](#14-admin-panel)
15. [Deployment](#15-deployment)
16. [Scripts](#16-scripts)
17. [Design Patterns](#17-design-patterns)

---

## 1. Project Overview

Zyphron Free Dash is a full-stack game server hosting dashboard with Pterodactyl Panel integration. Users earn credits (CR) through an AFK system, daily logins, and achievements — then spend them to create and manage game servers.

**Key capabilities:**
- User registration with Discord OAuth linking
- AFK credit earning system (browser tab-based)
- Game server creation, upgrade, renewal via Pterodactyl Panel API
- Support ticket system
- Role-based access control (RBAC)
- Achievements & daily login bonuses
- Full admin panel with 40+ management views

---

## 2. Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | NestJS 11, TypeORM, TypeScript |
| Frontend | Vue 3 (Composition API), Pinia, Vue Router |
| Styling | Tailwind CSS v4 |
| Build | Vite 8 |
| Database | SQLite (dev) / MySQL (prod) |
| Auth | JWT (7-day expiry), bcrypt |
| External | Pterodactyl Panel API, Discord OAuth2/Bot, Nodemailer SMTP |
| Deployment | PM2 |

---

## 3. Project Structure

```
zyphron-free-dash/
├── src/                         # Backend (NestJS)
│   ├── main.ts                  # App bootstrap
│   ├── app.module.ts            # Root module (24 modules)
│   ├── app.controller.ts        # Public endpoints (/config, /health, /public-settings)
│   ├── entities/                # 29 TypeORM entities
│   ├── auth/                    # JWT auth, register, login (@Global)
│   ├── admin/                   # Admin panel backend
│   ├── servers/                 # Server CRUD + lifecycle
│   ├── afk/                     # AFK coin earning
│   ├── store/                   # Upgrade/renew system
│   ├── economy/                 # Transaction history
│   ├── achievements/            # Achievement system
│   ├── tickets/                 # Support tickets
│   ├── products/                # Product management
│   ├── roles/                   # RBAC
│   ├── vouchers/                # Voucher system
│   ├── coupons/                 # Coupon system
│   ├── partners/                # Partner discounts
│   ├── payments/                # Payment records
│   ├── api-keys/                # Application API keys
│   ├── activity-logs/           # Audit logging
│   ├── useful-links/            # External links
│   ├── preferences/             # User theme/locale
│   ├── discord/                 # Discord OAuth2 + Bot
│   ├── pterodactyl/             # Pterodactyl API client
│   ├── mail/                    # Email (Nodemailer)
│   └── supabase/                # Supabase client (optional)
│
├── client/                      # Frontend (Vue 3)
│   ├── main.js                  # Entry point, theme init
│   ├── style.css                # Global CSS (dark/light themes)
│   ├── api/client.js            # Axios with JWT interceptor
│   ├── router/index.js          # 40+ routes
│   ├── stores/                  # Pinia stores (auth, servers, afk, store, admin)
│   ├── layouts/                 # MainLayout, AdminLayout
│   ├── views/                   # 19 user views
│   └── views/admin/             # 40 admin views
│
├── .env                         # Environment config
├── package.json                 # Dependencies & scripts
├── ecosystem.config.js          # PM2 config
└── zyphron.db                   # SQLite database
```

---

## 4. Getting Started

### Prerequisites
- Node.js 18+
- npm
- Pterodactyl Panel with Application API key
- Discord Bot + OAuth2 app (optional)

### Installation

```bash
git clone git@github.com:SirYadav1/zyphron-free-dash.git
cd zyphron-free-dash
npm install
```

### Setup

1. Copy `.env.example` to `.env` and fill in required values
2. Start development server:
```bash
npm run dev
```
3. Open browser at `http://localhost:5173`
4. Setup first admin:
```bash
curl -X POST http://localhost:3000/api/auth/setup-admin \
  -H "Content-Type: application/json" \
  -d '{"key":"YOUR_ADMIN_KEY","username":"admin","email":"admin@zyphron.cloud","password":"Admin123!"}'
```

### Build for Production

```bash
npm run build        # Builds both frontend + backend
npm run start:prod   # Run production server
```

---

## 5. Environment Variables

### Required

| Variable | Description | Example |
|----------|-------------|---------|
| `JWT_SECRET` | JWT signing secret | `your-super-secret-key` |
| `PTERODACTYL_PANEL_URL` | Panel URL | `https://panel.example.com` |
| `PTERODACTYL_API_KEY` | Application API Key (ptla_) | `ptla_...` |
| `ADMIN_KEY` | One-time admin setup key | `your-admin-key` |

### Optional

| Variable | Description | Default |
|----------|-------------|---------|
| `JWT_EXPIRES_IN` | Token expiry | `7d` |
| `PORT` | Server port | `3000` |
| `NODE_ENV` | Environment | `development` |
| `FRONTEND_URL` | CORS origin | `http://localhost:5173` |
| `MAIL_HOST` | SMTP host | `smtp.gmail.com` |
| `MAIL_PORT` | SMTP port | `587` |
| `MAIL_USER` | SMTP username | - |
| `MAIL_PASS` | SMTP password | - |
| `MAIL_FROM` | Sender email | `noreply@zyphron.com` |
| `DISCORD_CLIENT_ID` | Discord OAuth client ID | - |
| `DISCORD_CLIENT_SECRET` | Discord OAuth secret | - |
| `DISCORD_CALLBACK_URL` | OAuth callback | `http://localhost:3000/api/discord/callback` |
| `DISCORD_BOT_TOKEN` | Discord bot token | - |
| `DISCORD_GUILD_ID` | Discord server ID | - |
| `DB_TYPE` | Database type | `sqlite` |
| `DB_HOST` | MySQL host | `localhost` |
| `DB_PORT` | MySQL port | `3306` |
| `DB_USER` | MySQL user | `root` |
| `DB_PASSWORD` | MySQL password | - |
| `DB_NAME` | MySQL database | `zyphron` |

### Database Settings (stored in `settings` table, configurable via admin panel)

| Key | Default | Description |
|-----|---------|-------------|
| `daily_login_bonus` | 25 | Coins per daily login |
| `discord_link_bonus` | 50 | Coins for Discord link |
| `afk_coins_per_minute` | 10 | AFK earning rate |
| `afk_time_interval_minutes` | 1 | AFK earning interval |
| `server_validity_days` | 7 | Server lifetime in days |
| `renewal_coins` | 50 | Renewal cost |
| `ram_cost_per_mb` | 2 | RAM upgrade cost per MB |
| `cpu_cost_per_percent` | 5 | CPU upgrade cost per % |
| `disk_cost_per_mb` | 1 | Disk upgrade cost per MB |
| `max_servers_total` | 100 | Global server limit |
| `max_servers_per_user` | 3 | Per-user server limit |
| `pterodactyl_panel_url` | - | Panel URL (DB override) |
| `pterodactyl_api_key` | - | API key (DB override, hidden from API) |
| `panel_type` | pterodactyl | Panel type |
| `default_node_id` | 6 | Default Pterodactyl node |
| `default_egg_id` | 2 | Default egg |
| `default_nest_id` | 1 | Default nest |
| `default_ram_mb` | 2048 | Default RAM |
| `default_cpu_percent` | 100 | Default CPU |
| `default_disk_mb` | 5120 | Default disk |
| `default_docker_image` | ghcr.io/pterodactyl/yolks:java_25 | Default Docker image |

---

## 6. Database Schema

### Core Entities

#### `users`
| Column | Type | Notes |
|--------|------|-------|
| id | text (PK) | UUID |
| username | text (unique) | |
| first_name | text? | |
| last_name | text? | |
| email | text? (unique) | |
| password_hash | text? | bcrypt |
| coins | int | Default 100 |
| discord_id | text? | |
| discord_username | text? | |
| discord_left_at | text? | |
| pterodactyl_user_id | int? | |
| pterodactyl_username | text? | |
| is_banned | int | Default 0 |
| banned_at | text? | |
| ban_reason | text? | |
| ip_address | text? | |
| device_fingerprint | text? | |
| is_admin | int | Default 0 |
| email_verified | int | Default 0 |
| created_at | text | |
| updated_at | text | |

#### `servers`
| Column | Type | Notes |
|--------|------|-------|
| id | int (PK, auto) | |
| user_id | text (FK → users) | |
| template_id | int? (FK → server_templates) | |
| pterodactyl_server_id | text? | |
| server_name | text | |
| ram_mb | int | |
| cpu_percent | int | |
| disk_mb | int | |
| status | text | Default 'active' |
| expires_at | text? | |
| last_active_at | text? | |
| created_at | text | |
| updated_at | text | |

#### `server_templates`
| Column | Type | Notes |
|--------|------|-------|
| id | int (PK, auto) | |
| name | text | |
| node_id | int | Pterodactyl node |
| egg_id | int | Pterodactyl egg |
| nest_id | int | Pterodactyl nest |
| ram_mb, cpu_percent, disk_mb | int | |
| databases_limit, allocations_limit, backup_limit | int | |
| dedicated_ip | int | |
| docker_image | text? | |
| is_active | int | Default 1 |

### Economy Entities

#### `transactions`
| Column | Type | Notes |
|--------|------|-------|
| id | int (PK, auto) | |
| user_id | text (FK → users) | |
| type | text | e.g. 'daily_bonus', 'server_create', 'upgrade' |
| amount | int | Positive = earn, negative = spend |
| description | text? | |
| created_at | text | |

#### `afk_sessions`
| Column | Type | Notes |
|--------|------|-------|
| id | int (PK, auto) | |
| user_id | text (FK → users) | |
| tab_id | text | Unique per session |
| total_earned | int | |
| total_visible_seconds | int | |
| is_active | int | Default 1 |
| started_at | text | |
| last_heartbeat_at | text? | |
| ended_at | text? | |

#### `daily_logins`
| Column | Type | Notes |
|--------|------|-------|
| id | int (PK, auto) | |
| user_id | text (FK → users) | |
| login_date | text | Unique with user_id |
| coins_earned | int | Default 0 |
| created_at | text | |

#### `user_achievements`
| Column | Type | Notes |
|--------|------|-------|
| id | int (PK, auto) | |
| user_id | text (FK → users) | |
| achievement_key | text | Unique with user_id |
| achieved_at | text | |
| coins_rewarded | int | Default 0 |

### Settings & Config

#### `settings`
| Column | Type | Notes |
|--------|------|-------|
| id | int (PK, auto) | |
| key | text (unique) | |
| value | text | |

#### `panel_config`
| Column | Type | Notes |
|--------|------|-------|
| id | int (PK, auto) | |
| panel_type | text | Default 'pterodactyl' |
| panel_url | text | |
| api_key | text | |
| is_active | int | Default 1 |

### Roles & Permissions

#### `roles` | `permissions` | `role_permissions` | `user_roles`
Standard many-to-many RBAC tables linking users to roles and roles to permissions.

### Ticket System

#### `tickets`
| Column | Type | Notes |
|--------|------|-------|
| id | int (PK, auto) | |
| ticket_id | text (unique) | Display ID |
| user_id | text (FK → users) | |
| title | text | |
| ticketcategory_id | int? | |
| priority | text | Default 'Medium' |
| status | text | Default 'Open' |
| server | text? | Optional server reference |

#### `ticket_comments` | `ticket_categories` | `ticket_blacklist`

### Other Entities

- `products` — Server product templates
- `shop_products` — Store items for purchase
- `vouchers` / `user_vouchers` — Credit vouchers
- `coupons` — Discount coupons
- `partner_discounts` — Partner referral discounts
- `payments` / `invoices` — Payment records
- `application_apis` — API keys for external access
- `activity_logs` — Audit trail
- `useful_links` — External links displayed in UI

---

## 7. Authentication System

### Flow

1. **Register** → POST `/api/auth/register`
   - Creates user with UUID, bcrypt-hashed password
   - Awards 100 starting coins
   - Auto-creates Pterodactyl user (non-blocking)
   - Returns JWT (7-day expiry)

2. **Login** → POST `/api/auth/login`
   - Validates email/password
   - Checks ban status
   - Awards daily login bonus (once per day)
   - Returns JWT + user profile

3. **JWT Guard** (`JwtAuthGuard`)
   - Verifies `Authorization: Bearer <token>` header
   - Sets `request.user = { id: payload.sub }`

4. **Admin Guard** (`AdminGuard`)
   - Requires JwtAuthGuard + `user.is_admin === 1` (DB check)

5. **Admin Setup** → POST `/api/auth/setup-admin`
   - One-time setup using `ADMIN_KEY` from `.env`
   - Uses `timingSafeEqual` for secure comparison
   - Only works if no admin exists

6. **Discord OAuth2** → GET `/api/discord/link`
   - JWT passed as `state` parameter
   - Redirects to Discord with `identify` + `guilds.join` scopes
   - On callback: verifies guild membership, awards 50 CR bonus

### Security Features
- Rate limiting: 100 req/60s global, stricter on auth endpoints
- Bcrypt password hashing (10 rounds)
- Timing-safe admin key comparison
- JWT with 7-day expiry
- CORS restricted to `FRONTEND_URL`

---

## 8. API Endpoints

### Public (No Auth)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/config` | Public config (siteName, panelUrl) |
| GET | `/api/public-settings` | Login image, favicon |
| GET | `/api/health` | Health check |
| GET | `/api/useful-links` | Useful links |

### Auth (`/api/auth`)

| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/auth/register` | Register new user |
| POST | `/api/auth/login` | Login → JWT |
| POST | `/api/auth/verify-email` | Email verification (stub) |
| POST | `/api/auth/resend-verification` | Resend verification |
| GET | `/api/auth/me` | Get profile |
| PUT | `/api/auth/me` | Update profile |
| POST | `/api/auth/setup-admin` | Setup first admin |

### Discord (`/api/discord`)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/discord/link` | Start OAuth |
| GET | `/api/discord/callback` | OAuth callback |
| POST | `/api/discord/unlink` | Unlink + suspend servers |
| GET | `/api/discord/status` | Link status |

### Servers (`/api/servers`)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/servers/blueprint` | Default server config |
| GET | `/api/servers` | List user's servers |
| GET | `/api/servers/stats` | Server stats |
| POST | `/api/servers` | Create server (100 CR) |
| GET | `/api/servers/:id` | Server detail |
| PUT | `/api/servers/:id` | Update server |
| DELETE | `/api/servers/:id` | Delete server |

### AFK (`/api/afk`)

| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/afk/start` | Start AFK session |
| POST | `/api/afk/heartbeat` | Send heartbeat |
| POST | `/api/afk/stop` | End session |
| GET | `/api/afk/stats` | AFK stats |

### Store (`/api/store`)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/store/:serverId` | Upgrade options + pricing |
| POST | `/api/store/:serverId/upgrade` | Upgrade RAM/CPU/Disk |
| POST | `/api/store/:serverId/renew` | Renew server |

### Other User Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/transactions` | Transaction history |
| GET | `/api/achievements` | User achievements |
| GET | `/api/achievements/available` | All achievements |
| GET | `/api/achievements/progress` | Achievement progress |
| POST | `/api/achievements/check` | Check achievements |
| GET | `/api/tickets` | User tickets |
| POST | `/api/tickets` | Create ticket |
| GET | `/api/tickets/:id` | Ticket detail |
| POST | `/api/tickets/:id/reply` | Reply to ticket |
| POST | `/api/tickets/:id/close` | Close ticket |
| GET | `/api/preferences` | User preferences |
| PUT | `/api/preferences` | Update preferences |

### Admin (`/api/admin`) — JWT + Admin Required

**Users:**
| GET/PUT | `/api/admin/users`, `/api/admin/users/:id` | List/Update users |
| POST | `/api/admin/users/:id/ban`, `/api/admin/users/:id/unban` | Ban/Unban |

**Servers:**
| GET | `/api/admin/servers` | List all servers |
| POST | `/api/admin/servers/:id/suspend`, `unsuspend`, `delete` | Manage servers |
| GET | `/api/admin/servers/expired` | Expired servers |

**Settings:**
| GET/PUT | `/api/admin/settings`, `/api/admin/settings/:key` | Manage settings |

**Pterodactyl:**
| GET | `/api/admin/pterodactyl/nodes`, `nests`, `nests/:id/eggs` | Panel data |
| GET/PUT | `/api/admin/pterodactyl/config` | Panel config |
| POST | `/api/admin/pterodactyl/test` | Test connection |

**Roles:**
| CRUD | `/api/admin/roles` | Manage roles |
| POST/DELETE | `/api/admin/roles/:id/assign` | Assign/Remove role |

**Products/Store/Vouchers/Coupons/Partners:** Full CRUD on each  
**Tickets:** List, view, reply, blacklist management  
**Activity Logs:** GET `/api/admin/activitylogs`  
**API Keys:** CRUD on `/api/admin/api`  
**Useful Links:** CRUD on `/api/admin/useful-links`

---

## 9. Backend Architecture

### Modules (24 total)

| Module | Purpose |
|--------|---------|
| AuthModule (@Global) | JWT auth, register, login |
| AdminModule | Admin panel backend |
| ServersModule | Server CRUD + lifecycle |
| AfkModule | AFK coin earning |
| StoreModule | Upgrade/renew |
| EconomyModule | Transaction history |
| AchievementsModule | Achievement system |
| TicketsModule | Support tickets |
| ProductsModule | Product management |
| RolesModule | RBAC |
| VouchersModule | Coin vouchers |
| CouponsModule | Discount coupons |
| PartnersModule | Partner discounts |
| PaymentsModule | Payment records |
| ApiKeysModule | Application API keys |
| ActivityLogsModule | Audit logging |
| UsefulLinksModule | External links |
| PreferencesModule | User preferences |
| DiscordModule | Discord OAuth2 + Bot |
| PterodactylModule | Panel API client |
| MailModule | Email service |
| SupabaseModule (@Global) | Supabase client (optional) |

### Guards

| Guard | Description |
|-------|-------------|
| JwtAuthGuard | Verifies JWT, checks user exists & not banned |
| AdminGuard | Requires JWT + `is_admin === 1` |

### Global Config
- `ValidationPipe`: whitelist, forbidNonWhitelisted, transform
- `ThrottlerGuard`: 100 req/60s
- All routes prefixed with `/api`

---

## 10. Frontend Architecture

### Tech
- Vue 3 with `<script setup>` (Composition API)
- Pinia for state management
- Vue Router with route guards
- Tailwind CSS v4 for styling
- Axios with JWT interceptor

### Stores

| Store | Key State | Actions |
|-------|-----------|---------|
| auth | user, loading | register, login, logout, fetchProfile |
| servers | servers, blueprint, stats | fetchServers, createServer, deleteServer |
| afk | sessionId, isActive, totalEarned | startSession, stopSession, heartbeat |
| store | server, pricing | fetchOptions, upgrade, renew |
| admin | stats, users, servers, settings, roles | CRUD for all admin resources |

### Router Guards
- `requiresAuth` → redirects to `/login` if no token
- `guest` → redirects to `/home` if logged in
- `requiresAdmin` → checks `to.matched` for admin meta, verifies `authStore.isAdmin`

### Theming
- Dark mode (default) + Light mode
- Theme stored in `localStorage('zyphron_theme')`
- Applied immediately on page load (before Vue mounts)
- CSS custom properties for colors

### Key Views

**User Views (19):** Login, Register, Dashboard, Servers, CreateServer, ServerSettings, Store, StoreOverview, Tickets (list/create/show), Profile, Preferences, Settings, AFK, Transactions, VerifyEmail, VerifyPending

**Admin Views (40):** Dashboard, Users (list/show/edit), Servers (list/edit), Products (list/create/edit), Store (list/create/edit), Vouchers (list/create/edit/users), Partners (list/create/edit/users), Coupons (list/create/edit), Payments, Roles (list/create/edit/users), Settings, API Keys (list/create/edit), Tickets (list/show/blacklist), ActivityLogs, UsefulLinks (list/create/edit)

---

## 11. Key Features

### AFK Credit Earning
- User starts session → gets `sessionId` + unique `tabId`
- Client sends heartbeat every 10 seconds with tab focus state
- Coins earned only when tab is visible
- Rate configurable: `afk_coins_per_minute` per `afk_time_interval_minutes`
- Anti-cheat: single session per user, 60s heartbeat timeout, tab validation
- Stale sessions cleaned every 30 seconds

### Server Management
- **Create:** Costs 100 CR. Requires Discord linked + Pterodactyl account
- **Blueprint:** Default specs from admin settings (node, nest, egg, RAM, CPU, disk)
- **Lifecycle:** Active → Suspended (ban/unlink/expiry) → Expired → Deleted (24h after expiry)
- **Limits:** `max_servers_per_user` (3), `max_servers_total` (100)

### Store/Upgrade
- Upgrade RAM, CPU, or Disk for coins
- Renew server validity for `renewal_coins` (50 CR)
- Changes synced to Pterodactyl Panel via API

### Achievement System (10 achievements)
| Key | Reward | Condition |
|-----|--------|-----------|
| first_login | 10 CR | First login |
| discord_linked | 50 CR | Discord linked |
| first_server | 100 CR | 1+ servers |
| five_servers | 200 CR | 5+ servers |
| ten_servers | 500 CR | 10+ servers |
| afk_hour | 50 CR | 1+ AFK hours |
| afk_day | 200 CR | 24+ AFK hours |
| coins_collector | 100 CR | 1000+ coins |
| coins_hoarder | 300 CR | 5000+ coins |
| weekly_login | 150 CR | 7 consecutive daily logins |

### Daily Login Bonus
- Awarded once per day on login
- Default 25 CR (configurable)
- Optimistic locking prevents race conditions
- Recorded in `daily_logins` + `transactions`

### Support Tickets
- User creates tickets with title, priority, optional server reference
- Admin reply, close, manage categories
- Blacklist system (email/username/IP)

### RBAC
- Roles with 21 default permissions
- Permissions: users, servers, products, payments, settings, roles, tickets, vouchers (view/create/edit/delete)
- Assign/unassign from admin panel

### Discord Integration
- OAuth2 linking with guild membership verification
- Bot checks membership every 5 minutes
- Auto-suspend servers on unlink or guild leave
- Auto-unsuspend on rejoin
- 50 CR bonus on first link

---

## 12. External Integrations

### Pterodactyl Panel API
- **Config:** `pterodactyl_panel_url` + `pterodactyl_api_key` in settings
- **Base:** `{panelUrl}/api/application/...`
- **Auth:** Bearer token (Application API Key, `ptla_` prefix)
- **Config cache:** 30-second TTL
- **Operations:** Create/delete users, create/suspend/unsuspend/delete servers, update server builds, get nodes/nests/eggs/allocations, transfer servers

### Discord API
- OAuth2: `identify` + `guilds.join` scopes
- Bot: Guild member list, membership verification
- Rate: Membership check every 5 minutes

### Email (SMTP)
- Gmail SMTP (`smtp.gmail.com:587`)
- Templates: verification, ban/unban, server suspend/unsuspend/delete
- Graceful: skips if `MAIL_USER`/`MAIL_PASS` not configured

---

## 13. Cron Jobs

| Job | Schedule | Module | Description |
|-----|----------|--------|-------------|
| checkExpiredServers | Every hour | ServerLifecycleService | Suspends expired servers; deletes servers expired >24h |
| cleanupOrphanedServers | Every 6 hours | ServerLifecycleService | Removes DB servers not on Pterodactyl |
| cleanupStaleSessions | Every 30 seconds | AfkService | Ends AFK sessions with no heartbeat for 60s |
| checkDiscordMembership | Every 5 minutes | DiscordService | Checks guild membership; suspends/unsuspends servers |

---

## 14. Admin Panel

### Access
- Login as admin user (`is_admin === 1`)
- Admin sidebar visible in MainLayout
- All admin routes under `/admin/*`

### Features
- **Dashboard:** Stats overview, quick actions, panel link
- **Users:** List, view detail, edit, ban/unban, manage roles
- **Servers:** List, edit, suspend/unsuspend, delete, check expiry
- **Products:** CRUD for server product templates
- **Store:** CRUD for shop products
- **Vouchers:** CRUD + view users who used them
- **Partners:** CRUD + view partner users
- **Coupons:** CRUD for discount coupons
- **Payments:** View payment records
- **Roles:** CRUD + assign/unassign to users
- **Settings:** All system settings, AFK rates, Pterodactyl config, branding
- **API Keys:** CRUD for application API keys
- **Tickets:** List, view, reply, manage categories & blacklist
- **Activity Logs:** View audit trail
- **Useful Links:** CRUD for external links

### Global Server Blueprint
- Admin configures default node, nest, egg, RAM, CPU, disk, Docker image
- All user-created servers use these defaults
- Can be loaded from Pterodactyl via "Load from Pterodactyl" button

---

## 15. Deployment

### PM2 (`ecosystem.config.js`)
```js
{
  name: 'zyphron',
  script: 'dist/src/main.js',
  env: { NODE_ENV: 'production' },
  env_file: '.env',
  instances: 1,
  autorestart: true,
  max_memory_restart: '1G',
}
```

### Production
```bash
npm run build
pm2 start ecosystem.config.js
```

### Docker (not included, but compatible)
- Build: `npm run build`
- Run: `node dist/src/main.js`
- Exposes port 3000

---

## 16. Scripts

| Script | Description |
|--------|-------------|
| `npm run dev` | Start backend + frontend in dev mode |
| `npm run dev:backend` | Backend only (port 3000) |
| `npm run dev:frontend` | Frontend only (port 5173) |
| `npm run build` | Build both for production |
| `npm run build:frontend` | Build frontend to `dist/client/` |
| `npm run build:backend` | Build backend to `dist/` |
| `npm run start:prod` | Run production server |
| `npm run lint` | ESLint with auto-fix |
| `npm run format` | Prettier formatting |
| `npm run test` | Jest tests |

---

## 17. Design Patterns

1. **Optimistic Locking:** All coin updates use `WHERE coins = :currentCoins` to prevent race conditions
2. **Transaction Logging:** Every coin change creates a `transactions` record
3. **Graceful Degradation:** Mail skips if unconfigured; Pterodactyl auto-registration is non-blocking
4. **Config Caching:** Pterodactyl config cached 30s; AFK rates cached 60s
5. **One-time Admin Setup:** `timingSafeEqual` for secure key comparison
6. **Non-blocking External Calls:** Pterodactyl/Discord failures logged but don't crash the app
7. **Optimistic UI:** Frontend shows loading states and handles errors inline

---

*Documentation generated for Zyphron Free Dash — github.com/SirYadav1/zyphron-free-dash*
