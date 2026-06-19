# Zyphron Free Dash

> Open-source game server hosting dashboard with Pterodactyl Panel integration, credit economy, and AFK earning system.

![Stack](https://img.shields.io/badge/NestJS-11-red?logo=nestjs)
![Stack](https://img.shields.io/badge/Vue-3-42b883?logo=vuedotjs)
![Stack](https://img.shields.io/badge/TypeORM-0.3-blue)
![Stack](https://img.shields.io/badge/Tailwind_CSS-v4-38bdf8?logo=tailwindcss)
![License](https://img.shields.io/badge/License-MIT-green)

---

## What is this?

Zyphron Free Dash is a full-stack web application that acts as a client area for **Pterodactyl Panel**. Users earn credits (CR) through an AFK browser-based system, daily logins, and achievements — then spend those credits to create and manage game servers (Minecraft, Node.js, etc.) hosted on Pterodactyl.

**Live Demo:** [console.zyphron.cloud](https://console.zyphron.cloud)

---

## Features

### For Users
- **AFK Credit Earning** — Keep browser tab open to earn CR (configurable rates)
- **Server Management** — Create, upgrade, renew, and delete game servers
- **Discord Integration** — Link Discord account for guild-based server access
- **Support Tickets** — Submit and track support requests
- **Achievements** — Unlock badges and earn bonus credits
- **Daily Login Bonus** — Get CR just for logging in
- **Dark/Light Theme** — Full theme support with system preference detection

### For Admins
- **Full Admin Panel** — 40+ management views
- **User Management** — View, edit, ban/unban users
- **Server Control** — Suspend, unsuspend, delete servers
- **Global Blueprint** — Configure default server specs (RAM, CPU, disk, node, egg)
- **Product Management** — Create and manage server product templates
- **Voucher & Coupon System** — Distribute credits and discounts
- **RBAC** — Role-based access control with 21 permissions
- **Settings Panel** — Configure AFK rates, server limits, Pterodactyl connection
- **Activity Logs** — Full audit trail of admin actions
- **API Keys** — Generate application API keys for external integrations

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | [NestJS 11](https://nestjs.com) + TypeScript |
| Frontend | [Vue 3](https://vuejs.org) (Composition API) + [Pinia](https://pinia.vuejs.org) |
| Styling | [Tailwind CSS v4](https://tailwindcss.com) |
| Build | [Vite 8](https://vitejs.dev) |
| Database | SQLite (dev) / MySQL (prod) via [TypeORM](https://typeorm.io) |
| Auth | JWT + bcrypt |
| External | [Pterodactyl Panel API](https://pterodactyl.io), Discord OAuth2, Nodemailer |

---

## Quick Start

### Prerequisites
- Node.js 18+
- npm
- A running [Pterodactyl Panel](https://pterodactyl.io) instance

### 1. Clone & Install

```bash
git clone git@github.com:SirYadav1/zypher-admin-dashboard-free.git
cd zypher-admin-dashboard-free
npm install
```

### 2. Configure Environment

```bash
cp .env.example .env
```

Edit `.env` with your values:

```env
# Required
JWT_SECRET=your-super-secret-key
PTERODACTYL_PANEL_URL=https://panel.example.com
PTERODACTYL_API_KEY=ptla_your_api_key_here
ADMIN_KEY=your-admin-setup-key

# Optional
DISCORD_CLIENT_ID=your_discord_client_id
DISCORD_CLIENT_SECRET=your_discord_client_secret
DISCORD_BOT_TOKEN=your_bot_token
DISCORD_GUILD_ID=your_guild_id
MAIL_HOST=smtp.gmail.com
MAIL_USER=your@gmail.com
MAIL_PASS=your_app_password
```

### 3. Start Development

```bash
npm run dev
```

Open [http://localhost:5173](http://localhost:5173) in your browser.

### 4. Setup Admin Account

```bash
curl -X POST http://localhost:3000/api/auth/setup-admin \
  -H "Content-Type: application/json" \
  -d '{"key":"YOUR_ADMIN_KEY","username":"admin","email":"admin@example.com","password":"Admin123!"}'
```

Login with the credentials you just created.

---

## Project Structure

```
├── src/                    # Backend (NestJS)
│   ├── auth/               # JWT auth, register, login
│   ├── admin/              # Admin panel API
│   ├── servers/            # Server CRUD + lifecycle
│   ├── afk/                # AFK credit earning
│   ├── store/              # Upgrade/renew system
│   ├── achievements/       # Achievement system
│   ├── tickets/            # Support tickets
│   ├── roles/              # RBAC
│   ├── discord/            # Discord OAuth2 + Bot
│   ├── pterodactyl/        # Pterodactyl API client
│   ├── mail/               # Email service
│   └── entities/           # 29 TypeORM entities
│
├── client/                 # Frontend (Vue 3)
│   ├── views/              # 19 user views
│   ├── views/admin/        # 40 admin views
│   ├── stores/             # Pinia stores
│   ├── router/             # Vue Router
│   └── api/                # Axios client
│
└── docs/                   # Documentation
    └── FULL_DOCUMENTATION.md
```

---

## Available Scripts

| Script | Description |
|--------|-------------|
| `npm run dev` | Start backend + frontend in dev mode |
| `npm run dev:backend` | Backend only (port 3000) |
| `npm run dev:frontend` | Frontend only (port 5173) |
| `npm run build` | Build for production |
| `npm run start:prod` | Run production server |
| `npm run lint` | ESLint with auto-fix |

---

## API Overview

All endpoints are prefixed with `/api`. [Full API reference →](docs/FULL_DOCUMENTATION.md)

### Public
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/config` | Public configuration |
| GET | `/api/health` | Health check |

### Auth
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Register new user |
| POST | `/api/auth/login` | Login → JWT token |
| GET | `/api/auth/me` | Get profile |

### Servers (JWT required)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/servers` | List user servers |
| POST | `/api/servers` | Create server (100 CR) |
| DELETE | `/api/servers/:id` | Delete server |

### AFK (JWT required)
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/afk/start` | Start earning session |
| POST | `/api/afk/heartbeat` | Keep session alive |
| POST | `/api/afk/stop` | End session |

### Admin (JWT + Admin required)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/admin/stats` | Dashboard stats |
| GET/PUT | `/api/admin/users` | User management |
| GET | `/api/admin/servers` | Server management |
| GET/PUT | `/api/admin/settings` | System settings |

---

## Database

29 entities covering:

- **Users** — accounts, bans, Discord/Pterodactyl linking
- **Servers** — game server records, lifecycle status
- **Economy** — transactions, AFK sessions, daily logins, achievements
- **Support** — tickets, comments, categories, blacklist
- **RBAC** — roles, permissions, user-role assignments
- **Store** — products, shop items, vouchers, coupons, partners
- **Config** — settings, panel config, API keys, activity logs

[Full schema documentation →](docs/FULL_DOCUMENTATION.md#6-database-schema)

---

## Deployment

### PM2

```bash
npm run build
pm2 start ecosystem.config.js
```

### Environment

Set `NODE_ENV=production` in `.env` and configure your MySQL database:

```env
DB_TYPE=mysql
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=zyphron
```

---

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## License

This project is licensed under the MIT License.

---

## Credits

Built with [NestJS](https://nestjs.com), [Vue.js](https://vuejs.org), and [Pterodactyl Panel](https://pterodactyl.io).

> Made by [SirYadav1](https://github.com/SirYadav1)
