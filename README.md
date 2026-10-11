# TITAN — Shared Internet Bandwidth Usage Manager

[![MERN Stack](https://img.shields.io/badge/Stack-MERN%20%2B%20Sockets-teal.svg)](#technology-stack)
[![Tests Passing](https://img.shields.io/badge/Tests-29%2F29%20Passing-emerald.svg)](#testing)
[![PS-32 Compliant](https://img.shields.io/badge/Hackathon-PS--32%20%7C%20CBC048-cyan.svg)](#project-summary)

> **Problem Statement ID:** PS-32  
> **Team ID:** CBC048  
> **Project Type:** Shared Internet Bandwidth Usage Manager  

---

## 1. Project Summary

**TITAN** is an intelligent, policy-driven bandwidth management platform designed to eliminate network congestion and resource starvation in shared environments (university dorms, corporate campuses, research labs). 

TITAN allows administrators to define uplink capacities and emergency buffers while providing users with priority-driven bandwidth allocation. An autonomous, pure mathematical allocation engine balances critical demands (proctored exams, telehealth) against lower-tier traffic (backups, bulk downloads) using strict mathematical invariants.

> [!NOTE]  
> **PS-32 Simulation Notice:** As mandated by the problem statement rules, TITAN implements and demonstrates deterministic bandwidth allocation in **software**. Values on the dashboard represent policy-allocated Mbps. Real network gateway integration (e.g. Linux `tc`/QoS router drivers) is designed as a physical extension layer.

---

## 2. Key Features

- **Pure Mathematical Allocation Engine:**
  - **Policy A (FCFS):** First-Come, First-Served queue with timestamp and index tie-breaking.
  - **Policy B (Priority):** High > Medium > Low preemption with order preservation.
  - **Policy C (Adaptive Fair Sharing):** Minimum bandwidth floors, iterative water-filling surplus redistribution, and waiting-time aging to eliminate starvation.
- **Strict Invariant Guarantees:** Total allocated bandwidth is mathematically clamped: $\sum \text{Allocated} \le \text{TotalCapacity} - \text{ReservedBandwidth}$.
- **Real-Time Dashboards (Socket.IO):** Live telemetry pushed to room-authorized network dashboards immediately upon request submission, policy update, or cancellation.
- **Interactive Policy Simulator:** Side-by-side comparative simulation of FCFS, Priority, and Adaptive policies against live traffic data.
- **Role-Based Governance:** Secure JWT sessions, HTTP-only cookie support, password hashing with bcrypt, and per-network concurrency locking.

---

## 3. Technology Stack

- **Frontend:**
  - React 18 with Vite
  - React Router v6
  - Tailwind CSS (Navy / Charcoal / Restrained Teal operations theme)
  - Recharts (Bandwidth Bar Visualizers & Priority Donut Charts)
  - Lucide React Icons
  - Socket.IO Client
- **Backend:**
  - Node.js & Express
  - MongoDB with Mongoose ODM (includes automatic in-memory fallback for zero-setup local execution)
  - Socket.IO (with room-level authorization)
  - Zod Schema Validation
  - bcryptjs password hashing
  - express-rate-limit
- **Testing & Verification:**
  - Vitest & Supertest (29 unit & integration test cases passing)

---

## 4. Complete Project Structure

```
TITAN/
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── assets/
│   │   ├── components/
│   │   │   ├── common/         # Badge, Button, Modal, SimulatedBanner
│   │   │   ├── dashboard/      # StatCard, CapacityGauge, PolicyComparisonModal
│   │   │   ├── forms/          # CreateNetworkModal, BandwidthRequestModal, JoinNetworkModal
│   │   │   └── charts/         # BandwidthAllocationChart, PriorityPieChart
│   │   ├── layouts/            # Navbar, MainLayout
│   │   ├── pages/              # Landing, Login, Register, AdminDashboard, UserDashboard, etc.
│   │   ├── services/           # api.js, authService.js, networkService.js, etc.
│   │   ├── context/            # AuthContext.jsx, ToastContext.jsx
│   │   ├── hooks/              # useSocket.js
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── index.css
│   ├── .env.example
│   ├── package.json
│   ├── vite.config.js
│   └── index.html
├── backend/
│   ├── src/
│   │   ├── config/             # db.js, env.js
│   │   ├── models/             # User, Network, NetworkMember, BandwidthRequest, AllocationRun, AllocationHistory
│   │   ├── controllers/        # auth, network, member, request, allocation, dashboard
│   │   ├── routes/             # Express route declarations
│   │   ├── middleware/         # authenticate, authorize, validate, errorHandler, notFound
│   │   ├── services/           # allocationEngine.js (Pure), allocationService.js, networkService.js, etc.
│   │   ├── sockets/            # socketServer.js, socketAuth.js
│   │   ├── validators/         # Zod input schemas
│   │   ├── utils/              # ApiError.js, asyncHandler.js
│   │   ├── app.js
│   │   └── server.js
│   ├── tests/                  # allocationEngine.test.js, auth.test.js, network.test.js, requests.test.js
│   ├── scripts/                # seed.js (PS-32 Benchmark Demo Seed)
│   ├── .env.example
│   └── package.json
├── docs/
│   ├── ARCHITECTURE.md         # System design, C4 diagrams, concurrency model
│   ├── API.md                  # REST endpoints and request/response specifications
│   ├── ALGORITHM.md            # Pure math models, water-filling pseudocode, benchmark proof
│   ├── SECURITY.md             # RBAC, socket authorization, attack mitigations
│   └── DEMO_SCRIPT.md          # 5-minute hackathon judge walkthrough
├── .gitignore
├── .env.example
├── package.json                # Root npm workspaces
├── README.md
└── PROJECT_STATUS.md           # Implementation phase tracker
```

---

## 5. Prerequisites & Quick Start

### Prerequisites
- **Node.js:** v18.0.0 or later (Tested on Node v24)
- **npm:** v9.0.0 or later
- **MongoDB (Optional):** A local MongoDB instance or cloud URI. If no MongoDB is running, TITAN **automatically spins up an embedded in-memory MongoDB** so you can run the project with zero external setup!

### 1. Installation
Install all dependencies across root, backend, and frontend with a single command:
```bash
cd TITAN
npm install
```

### 2. Seed Demo Data
Pre-populate the database with the PS-32 acceptance benchmark scenario (100 Mbps network, 10 Mbps reserve, 3 users with 120 Mbps total demand):
```bash
npm run seed
```

### 3. Run Development Servers
Launch both the backend API (Port 5000) and frontend interface (Port 5173) concurrently:
```bash
npm run dev
```

Open your browser at: **`http://localhost:5173`**

---

## 6. Demo Accounts

Password for all pre-seeded accounts: **`TitanDemo123!`**

| Account | Email | Role | Scenario Details |
| :--- | :--- | :--- | :--- |
| **Administrator** | `admin@titan.local` | Admin | Network owner, manages policies and capacity |
| **User A (Alex)** | `alex@titan.local` | Member | 40 Mbps desired, **High Priority** (Proctored Exam) |
| **User B (Bella)** | `bella@titan.local` | Member | 30 Mbps desired, **Medium Priority** (Video Meeting) |
| **User C (Charlie)** | `charlie@titan.local` | Member | 50 Mbps desired, **Low Priority** (Dataset Download) |

*(Quick one-click fill buttons are available directly on the Sign-In page for instant testing).*

---

## 7. Testing & Build Verification

### Run Automated Test Suite
Execute the full test suite (pure allocation engine, auth, networks, requests):
```bash
npm test
```
**Results:** 4 test files, 29/29 tests passing.

### Run Production Build
Verify frontend compilation and bundle generation:
```bash
npm run build
```
Generates production assets in `frontend/dist/`.

---

## 8. Available Commands

| Command | Description |
| :--- | :--- |
| `npm run dev` | Starts backend and frontend concurrently |
| `npm run dev:backend` | Starts only the Express/Socket.IO backend server |
| `npm run dev:frontend` | Starts only the Vite frontend dev server |
| `npm test` | Runs the Vitest automated test suite |
| `npm run seed` | Seeds demo users, benchmark network, and initial requests |
| `npm run build` | Compiles production bundle for the frontend |

---

## 9. Limitations & Future Roadmap

1. **Hardware Driver Integration:** The current version calculates and demonstrates deterministic bandwidth allocations in software. Future versions will integrate physical router QoS gateways (such as Linux `tc` / `nftables` or OpenFlow APIs).
2. **Dynamic Uplink Probing:** Integrating active ping/SNMP telemetry to auto-adjust `totalCapacityMbps` dynamically based on ISP physical line fluctuations.
