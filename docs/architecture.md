# EZ-Ship Architecture Overview (MVP)

## Vision

MVP for a last-mile delivery and storage platform using convenience stores as micro-warehouses with driver-based delivery. **This MVP must be demonstration-ready for live partner conversations**, clearly showing the end-to-end workflow without requiring hypothetical explanations. The system demonstrates core business workflow, multi-role access, and shipment tracking with stable, uninterrupted demos.

## Core Flow

`Customer Books Storage` → `Store Partner Checks In Item + Photo` → `Store Marks Ready` → `Driver Accepts + Picks Up + Photo` → `Driver Delivers + Photo`

**Critical:** All handoffs between roles must be visible within the system through status changes or interface updates. The full lifecycle must be demonstrable without manual intervention or developer fixes.

---

## Tech Stack

### Frontend

- **Framework:** Next.js 14+ (App Router)
- **Styling:** TailwindCSS
- **Auth:** Clerk (with role metadata)
- **Deployment:** Vercel

### Backend

- **Runtime:** Cloudflare Workers
- **Database:** D1 (SQLite)
- **Storage:** R2 (photos)
- **Auth:** Clerk JWT verification

### Shared

- **TypeScript** end-to-end
- **Monorepo structure** (single Next.js app + Cloudflare Workers package)

---

## 3 Independent Modules (Parallel Development)

### Module A: Platform API & Data Layer

**Owner:** Developer 1  
**Tech:** Cloudflare Workers + D1 + R2  
**Deliverables:**

- REST API endpoints (`/v1/*`)
- D1 schema + migrations
- Clerk JWT verification middleware
- RBAC enforcement
- R2 signed upload/download URLs
- Status state machine logic
- Shared TypeScript types package

**Key Responsibilities:**

- User roles: `customer`, `store_staff`, `courier`, `admin`
- Item lifecycle management
- Photo upload orchestration
- Admin approval workflows
- Notifications feed (in-app)
- Pricing estimate calculation

---

### Module B: Customer Experience

**Owner:** Developer 2  
**Tech:** Next.js (App Router)  
**Routes:** `(customer)/*`  
**Deliverables:**

- Store search by postal code (mock geocoding for POC)
- Multi-item booking form (dimensions + weight)
- Booking detail page with status timeline
- Photo gallery (view uploaded check-in, pickup, delivery photos)
- In-app notifications feed UI

**Dependencies:**

- Module A: API endpoints + shared types
- Clerk: customer signup/login

**Key Pages:**

- `/customer/stores` - Search stores by postal code
- `/customer/bookings/new` - Create booking
- `/customer/bookings/[id]` - View booking status + photos
- `/customer/notifications` - Notification feed

---

### Module C: Operations Experience

**Owner:** Developer 3  
**Tech:** Next.js (App Router)  
**Routes:** `(store)/*`, `(courier)/*`, `(admin)/*`  
**Deliverables:**

- Store dashboard: inventory, check-in items with photo upload, mark ready
- Courier dashboard: available pickups, accept tasks, upload pickup/delivery photos
- Admin panel: approve stores/couriers, manage pricing rules, export CSV, admin management

**Dependencies:**

- Module A: API endpoints + shared types + R2 upload flow
- Clerk: role-based access control

**Key Pages:**

- `/store/dashboard` - Inventory + pending check-ins
- `/store/items/[id]/check-in` - Upload photo, confirm dims/weight
- `/courier/dashboard` - Available pickup tasks
- `/courier/tasks/[id]` - Pickup/delivery flow with photo
- `/admin/approvals` - Approve pending stores/couriers
- `/admin/stores` - Manage all stores
- `/admin/pricing` - Pricing rules configuration
- `/admin/export` - CSV export

---

## Merge Strategy & Contracts

### Contract 1: Item Status State Machine

**All modules MUST use these exact statuses:**

```
BOOKED → CHECKED_IN → READY_FOR_PICKUP → PICKED_UP → DELIVERED
                                      ↓
                                 CANCELLED (terminal state from any status)
```

**State transitions (enforced by Module A):**

- `BOOKED` → `CHECKED_IN`: Store uploads photo + confirms dims/weight
- `CHECKED_IN` → `READY_FOR_PICKUP`: Store or Admin marks ready
- `READY_FOR_PICKUP` → `PICKED_UP`: Courier uploads pickup photo
- `PICKED_UP` → `DELIVERED`: Courier uploads delivery photo
- Any → `CANCELLED`: Customer, Store, or Admin

### Contract 2: Role-Based Access Control

**Roles** (stored in Clerk `publicMetadata.role`):

- `customer` (default on signup)
- `store_staff` (requires admin approval)
- `courier` (requires admin approval)
- `admin` (first user becomes admin, can promote others)

**Permission Matrix:**
| Action | Customer | Store | Courier | Admin |
|--------|----------|-------|---------|-------|
| Create booking | ✓ | | | ✓ |
| Check in item | | ✓ | | ✓ |
| Mark ready | | ✓ | | ✓ |
| Accept pickup | | | ✓ | ✓ |
| Mark delivered | | | ✓ | ✓ |
| Approve users | | | | ✓ |
| Manage pricing | | | | ✓ |

### Contract 3: Shared Types Package

**Module A creates and exports:**

```typescript
// src/types/index.ts (shared across all modules)
export type UserRole = "customer" | "store_staff" | "courier" | "admin";
export type ItemStatus =
  | "BOOKED"
  | "CHECKED_IN"
  | "READY_FOR_PICKUP"
  | "PICKED_UP"
  | "DELIVERED"
  | "CANCELLED";
export type ApprovalStatus = "PENDING" | "APPROVED" | "REJECTED";

export interface User {
  id: string;
  clerkUserId: string;
  role: UserRole;
  email: string;
  createdAt: string;
}

export interface Store {
  id: string;
  name: string;
  postalCode: string;
  address: string;
  status: ApprovalStatus;
  ownerId: string;
  createdAt: string;
}

export interface Booking {
  id: string;
  customerId: string;
  storeId: string;
  storageStartAt: string;
  storageEndAt: string;
  priceEstimateCents: number;
  status: ItemStatus; // booking inherits status from items
  createdAt: string;
}

export interface Item {
  id: string;
  bookingId: string;
  storeId: string;
  lengthCm: number;
  widthCm: number;
  heightCm: number;
  weightKg: number;
  status: ItemStatus;
  assignedCourierId?: string;
  createdAt: string;
  updatedAt: string;
}

export interface ItemPhoto {
  id: string;
  itemId: string;
  photoType: "CHECK_IN" | "PICKUP" | "DELIVERY";
  r2Key: string;
  uploadedBy: string;
  uploadedAt: string;
}

export interface Notification {
  id: string;
  userId: string;
  type: string;
  title: string;
  message: string;
  isRead: boolean;
  createdAt: string;
}

// API Response types
export interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: string;
}
```

### Contract 4: R2 Upload Flow

**Mandatory 3-step flow for all photo uploads:**

1. **Request signed upload URL** (Module A endpoint)

   ```typescript
   POST /v1/uploads/sign
   Body: { itemId: string, photoType: 'CHECK_IN' | 'PICKUP' | 'DELIVERY' }
   Response: { uploadUrl: string, objectKey: string }
   ```

2. **Client uploads directly to R2** (Module B/C handles)

   ```typescript
   PUT <uploadUrl>
   Headers: { 'Content-Type': 'image/jpeg' }
   Body: <binary image data>
   ```

3. **Confirm upload & persist metadata** (Module A endpoint)
   ```typescript
   POST /v1/uploads/confirm
   Body: { objectKey: string, itemId: string, photoType: string }
   Response: { photoId: string }
   ```

### Contract 5: Environment Variables

**Shared across modules:**

**Clerk (all modules):**

- `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY`
- `CLERK_SECRET_KEY`

**Cloudflare (Module A):**

- `CLOUDFLARE_ACCOUNT_ID`
- `CLOUDFLARE_D1_DATABASE_ID`
- `CLOUDFLARE_R2_BUCKET_NAME`
- `CLOUDFLARE_R2_ACCESS_KEY_ID`
- `CLOUDFLARE_R2_SECRET_ACCESS_KEY`

**API Base URL (Module B & C):**

- `NEXT_PUBLIC_API_BASE_URL` (e.g., `https://api.ezship.dev`)

---

## Repository Structure

```
ez-ship/
├── packages/
│   ├── api/                    # Module A: Cloudflare Workers
│   │   ├── src/
│   │   │   ├── index.ts        # Worker entry point
│   │   │   ├── routes/         # API route handlers
│   │   │   ├── middleware/     # Auth, RBAC
│   │   │   ├── services/       # Business logic
│   │   │   ├── db/             # D1 queries + migrations
│   │   │   └── types/          # Shared types (exported)
│   │   ├── wrangler.toml
│   │   └── package.json
│   │
│   └── types/                  # Shared types package (symlinked from api/src/types)
│       └── index.ts
│
├── apps/
│   └── web/                    # Module B + C: Next.js
│       ├── src/
│       │   ├── app/
│       │   │   ├── (customer)/     # Module B routes
│       │   │   ├── (store)/        # Module C routes
│       │   │   ├── (courier)/      # Module C routes
│       │   │   ├── (admin)/        # Module C routes
│       │   │   └── layout.tsx      # Root layout with Clerk
│       │   ├── components/
│       │   │   ├── customer/       # Module B components
│       │   │   └── ops/            # Module C components
│       │   ├── lib/
│       │   │   └── api-client.ts   # Fetch wrapper using shared types
│       │   └── middleware.ts       # Clerk route protection
│       └── package.json
│
├── docs/
│   ├── ARCHITECTURE.md         # This file
│   ├── API_CONTRACT.md         # Full API spec
│   ├── DATABASE_SCHEMA.md      # D1 schema
│   ├── MODULE_A.md             # Dev 1 spec
│   ├── MODULE_B.md             # Dev 2 spec
│   └── MODULE_C.md             # Dev 3 spec
│
└── package.json                # Root monorepo config
```

---

## Integration & Merge Plan

### Week 0-1: Foundation Setup (All devs collaborate)

- Initialize monorepo structure
- Set up Clerk project + roles
- Set up Cloudflare account (D1 + R2)
- Create shared types package
- Deploy "hello world" to Vercel + Workers

### Week 2-5: Parallel Development

**Module A (Dev 1):**

- D1 schema + migrations
- Core CRUD endpoints
- Status transition logic
- R2 upload flow
- Deploy to Workers

**Module B (Dev 2):**

- Customer signup flow
- Store search (mock data)
- Booking creation form
- Booking detail page
- Test against Module A staging API

**Module C (Dev 3):**

- Store/courier signup + approval UI
- Check-in flow + photo upload
- Courier task acceptance
- Admin dashboard
- Test against Module A staging API

### Week 6: Integration Week

- Dev 1 freezes API contract
- Dev 2 & 3 finalize integration tests
- Mock data seeding for demo
- End-to-end smoke tests

### Week 7-8: Polish & Demo

- Bug fixes
- UI polish
- Demo video recording
- Documentation

---

## Development Workflow

### Local Development

**Module A:**

```bash
cd packages/api
wrangler dev --local
# Runs on http://localhost:8787
```

**Module B & C:**

```bash
cd apps/web
npm run dev
# Runs on http://localhost:3000
# Set NEXT_PUBLIC_API_BASE_URL=http://localhost:8787
```

### Database Migrations

```bash
cd packages/api
wrangler d1 execute <DB_NAME> --local --file=./migrations/001_init.sql
```

### Deployment

**Module A:**

```bash
wrangler deploy
```

**Module B & C:**

```bash
vercel deploy
```

---

## Critical Success Factors

1. **API contract first:** Module A publishes endpoint specs before Modules B/C start integration
2. **Shared types:** All modules import from same `@ezship/types` package
3. **Status transitions:** Use exact enum values; no hardcoded strings
4. **Photo uploads:** Always use 3-step signed URL flow
5. **Role enforcement:** Check roles in both frontend (UX) and backend (security)
6. **Mock data:** Seed realistic postal codes for store search testing

---

## Risk Mitigation

| Risk                         | Mitigation                                               |
| ---------------------------- | -------------------------------------------------------- |
| API changes break frontend   | Semantic versioning + changelog                          |
| Merge conflicts in routes    | Strict route group ownership                             |
| Photo upload race conditions | Confirm upload before status transition                  |
| Admin bootstrap exploited    | First-user-admin acceptable for POC; document for future |
| Type drift                   | Single source of truth in `packages/types`               |

---

## Post-POC Scaling Considerations

(Out of scope, but noted for future)

- Real geocoding (replace mock postal code matching)
- Email/SMS notifications (currently in-app only)
- Payment integration
- Background jobs (Cloudflare Queues)
- Real-time updates (WebSockets or polling)
- Photo compression/thumbnails
- Audit logs
- Multi-store management for franchises
