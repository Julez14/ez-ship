# Module C: Operations Experience (Store Partners + Drivers + Admin)

**Owner:** Developer 3  
**Tech Stack:** Next.js 14 (App Router), TypeScript, TailwindCSS, Clerk  
**Deployment:** Vercel (same app as Module B)  
**Estimated Effort:** 3-4 weeks

---

## Overview

You own all operational interfaces: store partner dashboard, driver dashboard, and admin panel.

**Store Partner Experience Priority:** Because decentralized storage is central to the E-Z Ship model, the store partner interface is **critically important**. It must be exceptionally clear and intuitive - simple enough to explain to a store owner in under two minutes during a live demo. Store partners must be able to: sign in, declare available storage space (square footage), view items currently in storage, and see when items are picked up. This module handles photo uploads, approval workflows, task management, and data exports. Your work shares the same Next.js app as Module B but uses separate route groups, ensuring no conflicts.

**Core Responsibilities:**

- **Store Partner Dashboard (HIGH PRIORITY):** Declare available storage space (square footage), inventory visibility, check-in items with photo upload, mark ready for pickup, see pickup confirmations
- **Driver Dashboard:** View available tasks with location context, accept pickups, upload pickup/delivery photos, delivery status updates

---

## Setup Checklist

### 1. Prerequisites

- [ ] Module B has completed: auth setup, API client, shared UI components
- [ ] Get API base URL from Module A dev
- [ ] Access to shared `@ezship/types` package
- [ ] Confirm role-based routing with Clerk

### 2. Project Structure (extends Module B)

```
apps/web/src/
├── app/
│   ├── (customer)/          # Module B (already exists)
│   ├── (store)/             # YOUR ROUTES: Store staff
│   │   ├── layout.tsx
│   │   ├── page.tsx         # Store dashboard
│   │   └── items/
│   │       └── [id]/
│   │           └── check-in/
│   │               └── page.tsx
│   ├── (courier)/           # YOUR ROUTES: Couriers
│   │   ├── layout.tsx
│   │   ├── page.tsx         # Courier dashboard
│   │   └── tasks/
│   │       └── [id]/
│   │           └── page.tsx  # Task detail (pickup/delivery)
│   └── (admin)/             # YOUR ROUTES: Admin panel
│       ├── layout.tsx
│       ├── page.tsx         # Admin dashboard
│       ├── approvals/
│       │   └── page.tsx
│       ├── stores/
│       │   └── page.tsx
│       ├── pricing/
│       │   └── page.tsx
│       └── export/
│           └── page.tsx
├── components/
│   └── ops/                 # YOUR COMPONENTS
│       ├── StoreInventoryList.tsx
│       ├── CheckInForm.tsx
│       ├── PhotoUpload.tsx
│       ├── TaskCard.tsx
│       ├── ApprovalCard.tsx
│       ├── PricingForm.tsx
│       └── ExportButton.tsx
└── lib/
    └── upload.ts            # R2 upload helper
```

---

## High-Level Tasks

### Phase 1: Store Dashboard (Week 1)

- [ ] **Store Layout**: Setup route protection for store_staff role with approval check
- [ ] **Store Dashboard**: Build inventory overview with stats (pending, in storage, ready)
- [ ] **Inventory List**: Display items by status with customer info
- [ ] **Check-in Form**: Create item check-in page with photo upload
- [ ] **Photo Upload Component**: Build reusable upload widget with preview
- [ ] **R2 Upload Helper**: Implement 3-step R2 flow (sign → upload → confirm)
- [ ] **Mark Ready**: Add button to transition items to READY_FOR_PICKUP

### Phase 2: Courier Dashboard (Week 2)

- [ ] **Courier Layout**: Setup route protection for courier role
- [ ] **Courier Dashboard**: Display available tasks and active assignments
- [ ] **Task Cards**: Show pickup location, item count, and accept button
- [ ] **Task Detail Page**: Build pickup/delivery flow with photo upload
- [ ] **Accept Task**: Implement task assignment workflow
- [ ] **Complete Pickup**: Upload photo and transition items to PICKED_UP
- [ ] **Complete Delivery**: Upload photo and transition items to DELIVERED

### Phase 3: Admin Panel (Week 3-4)

- [ ] **Admin Layout**: Setup route protection for admin role only
- [ ] **Admin Dashboard**: Display stats overview and quick action cards
- [ ] **Approvals Page**: List pending stores and couriers for review
- [ ] **Approval Cards**: Build approve/reject interface with reason field
- [ ] **Stores Management**: View and edit all registered stores
- [ ] **Pricing Management**: CRUD interface for pricing rules
- [ ] **Export Page**: Build CSV export for bookings and items with date filters
- [ ] **User Promotion**: Add ability to promote users to admin role

---

## Testing Checklist

- [ ] Store staff can check in items with photo upload
- [ ] Mark ready for pickup works
- [ ] Courier can view and accept tasks
- [ ] Courier can upload pickup and delivery photos
- [ ] Admin can approve/reject stores and couriers
- [ ] Admin can update pricing rules
- [ ] Admin can export CSV files
- [ ] Role-based routing works (redirects if wrong role)
- [ ] Photo upload to R2 works end-to-end
- [ ] All forms validate input correctly

---

## Deployment

- [ ] Coordinate with Module B to merge route groups
- [ ] Ensure no route conflicts between modules
- [ ] Test role-based redirects
- [ ] Deploy to Vercel with production environment
- [ ] Verify R2 photo uploads work in production

---

## Success Criteria

- [ ] All store, courier, and admin pages implemented
- [ ] Photo upload flow tested with R2
- [ ] Admin approval workflows functional
- [ ] CSV export downloads correctly
- [ ] Role-based access enforced
- [ ] Clean, consistent UI with TailwindCSS
- [ ] Deployed to Vercel and integrated with Module B

- [ ] Store staff can check in items with photo upload
- [ ] Mark ready for pickup works
- [ ] Courier can view and accept tasks
- [ ] Courier can upload pickup and delivery photos
- [ ] Admin can approve/reject stores and couriers
- [ ] Admin can update pricing rules
- [ ] Admin can export CSV files
- [ ] Role-based routing works (redirects if wrong role)
- [ ] Photo upload to R2 works end-to-end
- [ ] All forms validate input correctly

---

## Success Criteria

- [ ] All store, courier, and admin pages implemented
- [ ] Photo upload flow tested with R2
- [ ] Admin approval workflows functional
- [ ] CSV export downloads correctly
- [ ] Role-based access enforced
- [ ] Clean, consistent UI with TailwindCSS
- [ ] Deployed to Vercel and integrated with Module B

---

## Merge with Module B

**No conflicts expected** because:

- Module B owns `(customer)/*` routes
- Module C owns `(store)/*`, `(courier)/*`, `(admin)/*` routes
- Shared components in separate folders (`customer/` vs `ops/`)

**Shared code:**

- API client (already created by Module B)
- UI components (Button, Input, Card)
- Layout structure pattern

**Coordinate on:**

- Navigation component structure
- Clerk role metadata format
- Error handling patterns
