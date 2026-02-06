# Module A: Platform API & Data Layer

**Owner:** Developer 1  
**Tech Stack:** Cloudflare Workers, D1 (SQLite), R2, TypeScript  
**Deployment:** Cloudflare Workers  
**Estimated Effort:** 3-4 weeks

---

## Overview

You own the entire backend platform: API, database, file storage, and business logic. This module serves as the foundation for the MVP, enabling multi-role access, shipment tracking, and data capture. Modules B and C (frontend Next.js apps) depend on your API contract, so delivering stable endpoints early is critical.

**Acceptable Simulations for MVP:** Simulated payments, simulated driver availability, manual status changes, hardcoded routes, and mock notifications are explicitly acceptable. The focus is on realistic user experience for demonstrations, not production-grade automation.

**Core Responsibilities:**

- REST API with JWT authentication
- Role-based access control (RBAC)
- Item lifecycle state machine
- Photo upload/download via R2
- Admin approval workflows
- In-app notifications system
- Database schema + migrations

---

## Setup Checklist

### 1. Cloudflare Account Setup

- [ ] Create Cloudflare account
- [ ] Create D1 database: `ezship-db`
- [ ] Create R2 bucket: `ezship-photos` (private)
- [ ] Generate R2 access keys
- [ ] Note account ID and resource IDs

### 2. Clerk Setup (Shared with Frontend Devs)

- [ ] Create Clerk application
- [ ] Enable email/password authentication
- [ ] Add custom metadata fields: `role`, `approvalStatus`
- [ ] Share publishable key with frontend devs
- [ ] Save secret key for Workers

### 3. Project Initialization

- [ ] Initialize Cloudflare Workers project with Wrangler
- [ ] Install dependencies: Hono, Clerk auth, TypeScript
- [ ] Configure wrangler.toml with D1 and R2 bindings

### 4. Initialize Database

- [ ] Run D1 migrations locally and in production
- [ ] Seed comprehensive demo data for stable live demonstrations (test accounts for all roles, sample stores, sample bookings at various stages)

---

## Project Structure

```
packages/api/
├── src/
│   ├── index.ts              # Worker entry point, Hono app setup
│   ├── routes/
│   │   ├── users.ts          # /v1/users/* and /v1/me
│   │   ├── stores.ts         # /v1/stores/*
│   │   ├── bookings.ts       # /v1/bookings/*
│   │   ├── items.ts          # /v1/items/*
│   │   ├── tasks.ts          # /v1/tasks/*
│   │   ├── uploads.ts        # /v1/uploads/*
│   │   ├── notifications.ts  # /v1/notifications/*
│   │   └── admin.ts          # /v1/admin/*
│   ├── middleware/
│   │   ├── auth.ts           # Clerk JWT verification
│   │   ├── rbac.ts           # Role-based access control
│   │   └── errorHandler.ts   # Global error handling
│   ├── services/
│   │   ├── authService.ts    # User registration, role management
│   │   ├── bookingService.ts # Booking creation, pricing logic
│   │   ├── itemService.ts    # Item state machine, transitions
│   │   ├── taskService.ts    # Courier task management
│   │   ├── uploadService.ts  # R2 signed URLs
│   │   └── notificationService.ts  # Create notifications
│   ├── db/
│   │   ├── schema.ts         # Type definitions matching DB
│   │   ├── queries.ts        # Reusable SQL queries
│   │   └── migrations/       # SQL migration files
│   ├── lib/
│   │   ├── validation.ts     # Input validation helpers
│   │   ├── pricing.ts        # Pricing calculation
│   │   └── utils.ts          # UUID generation, date helpers
│   └── types/
│       └── index.ts          # Shared types (exported to frontend)
├── migrations/
│   └── 001_init.sql
├── wrangler.toml
├── package.json
└── tsconfig.json
```

---

## High-Level Tasks

### Phase 1: Foundation (Week 1)

- [ ] **Setup Hono Router**: Configure Worker entry point with CORS and Clerk middleware
- [ ] **Auth Middleware**: Implement JWT verification and role-based access control
- [ ] **Database Schema**: Apply D1 migrations from DATABASE_SCHEMA.md
- [ ] **Shared Types Package**: Export TypeScript types for frontend consumption

### Phase 2: Core API (Week 2)

- [ ] **User Registration**: Handle customer/store/courier signup with approval flows
- [ ] **Store Search**: Implement postal code-based store lookup
- [ ] **Booking Creation**: Multi-item booking with pricing calculation
- [ ] **Pricing Service**: Calculate storage fees based on volume + weight + duration

### Phase 3: Operations & State Machine (Week 3)

- [ ] **Item State Machine**: Implement status transitions with validation
- [ ] **Check-in Flow**: Store check-in with photo verification
- [ ] **R2 Upload**: 3-step signed URL flow (sign → upload → confirm)
- [ ] **Notifications**: Create in-app notifications for status changes

### Phase 4: Admin & Tasks (Week 4)

- [ ] **Admin Approvals**: Store and courier approval/rejection
- [ ] **Courier Tasks**: Task creation, assignment, and acceptance
- [ ] **Pickup/Delivery**: Photo-verified status transitions
- [ ] **Pricing Management**: Admin pricing rules CRUD
- [ ] **CSV Export**: Bookings and items data export

---

## Testing Strategy

- [ ] Setup local dev environment with `wrangler dev --local`
- [ ] Test endpoints with curl or Postman
- [ ] Write unit tests for pricing calculations
- [ ] Create integration test scripts for full booking flow
- [ ] Test R2 photo upload/download flow

---

## Deployment

- [ ] Create D1 database in Cloudflare
- [ ] Run migrations on production database
- [ ] Deploy Worker with `wrangler deploy`
- [ ] Set environment secrets via `wrangler secret put`
- [ ] Monitor logs with `wrangler tail`

---

## Critical Deliverables for Frontend Teams

- [ ] **Shared Types Package**: Up-to-date TypeScript types accessible to frontend
- [ ] **API Documentation**: OpenAPI/Swagger or Postman collection
- [ ] **Staging API URL**: Deployed endpoint for frontend integration
- [ ] **Test Accounts**: Sample users for each role (customer, store, courier, admin)

---

## Success Criteria

- [ ] All API endpoints support end-to-end workflow demonstration
- [ ] Database schema fully migrated with comprehensive demo data
- [ ] R2 upload/download working for photo verification
- [ ] State machine validates transitions and all handoffs are visible
- [ ] Role-based access enforced for all user types
- [ ] Admin approval flows functional
- [ ] Notifications created for key status changes
- [ ] API deployed and stable for live demonstrations
- [ ] Shared types package published
- [ ] System supports uninterrupted demos without manual intervention
- [ ] Frontend teams can authenticate and make requests
