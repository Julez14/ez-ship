# Module B: Customer Experience

**Owner:** Developer 2  
**Tech Stack:** Next.js 14 (App Router), TypeScript, TailwindCSS, Clerk  
**Deployment:** Vercel  
**Estimated Effort:** 2-3 weeks

---

## Overview

You own the customer-facing web application: finding stores, creating bookings, tracking item status, and viewing notifications. Your work is independent of Module C (Ops UI) but depends on Module A's API being deployed.

**Core Responsibilities:**

- Customer authentication (Clerk)
- Store search by postal code
- Multi-item booking form with dimensions/weight
- Booking detail page with status timeline
- Photo gallery (view check-in, pickup, delivery photos)
- In-app notifications feed

---

## Setup Checklist

### 1. Prerequisites

- [ ] Get Clerk publishable key from Module A dev
- [ ] Get API base URL (staging): `https://api-staging.ezship.dev`
- [ ] Install shared types: `npm link @ezship/types`
- [ ] Confirm Module A has deployed `/v1/me`, `/v1/stores`, `/v1/bookings` endpoints

### 2. Project Initialization

```bash
cd ez-ship/apps/web
npx create-next-app@latest . --typescript --tailwind --app --eslint
npm install @clerk/nextjs
npm install date-fns lucide-react
npm install @ezship/types  # or npm link @ezship/types
```

### 3. Environment Variables

Create `.env.local`:

```bash
# Clerk
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...

# API
NEXT_PUBLIC_API_BASE_URL=https://api-staging.ezship.dev

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

### 4. Configure Clerk

```bash
# middleware.ts
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server'

const isPublicRoute = createRouteMatcher(['/sign-in(.*)', '/sign-up(.*)'])

export default clerkMiddleware((auth, request) => {
  if (!isPublicRoute(request)) {
    auth().protect()
  }
})

export const config = {
  matcher: [
    '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
    '/(api|trpc)(.*)',
  ],
}
```

---

## Project Structure

```
apps/web/src/
├── app/
│   ├── (auth)/
│   │   ├── sign-in/[[...sign-in]]/page.tsx
│   │   └── sign-up/[[...sign-up]]/page.tsx
│   ├── (customer)/
│   │   ├── layout.tsx              # Customer-specific layout
│   │   ├── page.tsx                # Dashboard/home
│   │   ├── stores/
│   │   │   └── page.tsx            # Store search
│   │   ├── bookings/
│   │   │   ├── page.tsx            # Booking list
│   │   │   ├── new/
│   │   │   │   └── page.tsx        # Create booking form
│   │   │   └── [id]/
│   │   │       └── page.tsx        # Booking detail + timeline
│   │   └── notifications/
│   │       └── page.tsx            # Notification feed
│   ├── layout.tsx                  # Root layout with Clerk
│   └── page.tsx                    # Landing page (redirect to sign-in)
├── components/
│   ├── customer/
│   │   ├── StoreSearchForm.tsx
│   │   ├── StoreCard.tsx
│   │   ├── BookingForm.tsx
│   │   ├── ItemFormField.tsx
│   │   ├── BookingCard.tsx
│   │   ├── StatusTimeline.tsx
│   │   ├── PhotoGallery.tsx
│   │   └── NotificationItem.tsx
│   └── ui/
│       ├── Button.tsx
│       ├── Input.tsx
│       ├── Card.tsx
│       └── Badge.tsx               # For status badges
├── lib/
│   ├── api-client.ts               # Fetch wrapper with auth
│   ├── hooks/
│   │   ├── useBookings.ts
│   │   ├── useStores.ts
│   │   └── useNotifications.ts
│   └── utils.ts                    # Format dates, prices, etc.
└── types/
    └── index.ts                    # Re-export @ezship/types + local types
```

---

## High-Level Tasks

### Phase 1: Authentication & Layout (Days 1-2)

- [ ] **Root Layout**: Setup Clerk Provider and global styles
- [ ] **Customer Layout**: Implement route protection and navigation
- [ ] **Navigation Component**: Build header with user menu and links
- [ ] **API Client**: Create authenticated fetch wrapper for backend calls

### Phase 2: Store Search (Days 3-4)

- [ ] **Store Search Page**: Build postal code search interface
- [ ] **Store Search Form**: Implement search input with validation
- [ ] **Store Card**: Display store information with "Book Storage" CTA

### Phase 3: Booking Creation (Days 5-7)

- [ ] **Booking Form Page**: Create new booking route with store selection
- [ ] **Multi-Item Form**: Build dynamic form for adding/removing items
- [ ] **Dimensions Input**: Implement length/width/height/weight fields
- [ ] **Date Selection**: Add storage start/end date pickers
- [ ] **Form Validation**: Validate all fields before submission

### Phase 4: Booking Detail & Timeline (Days 8-10)

- [ ] **Booking Detail Page**: Display booking summary and items
- [ ] **Status Timeline**: Visualize item lifecycle with checkpoints
- [ ] **Photo Gallery**: Show check-in, pickup, and delivery photos
- [ ] **Status Badges**: Display current item status with colors

### Phase 5: Notifications (Days 11-12)

- [ ] **Notifications Page**: List all user notifications
- [ ] **Notification Items**: Show title, message, and timestamp
- [ ] **Mark as Read**: Update notification status on click
- [ ] **Deep Links**: Navigate to related booking from notification

---

## Testing Checklist

- [ ] Customer signup flow works
- [ ] Store search by postal code returns results
- [ ] Multi-item booking form validates input
- [ ] Booking creation shows price estimate
- [ ] Booking detail page displays status timeline
- [ ] Photos load with signed R2 URLs
- [ ] Notifications display and mark as read
- [ ] Navigation between pages works
- [ ] Responsive design on mobile

---

## Deployment

- [ ] Build and test locally with `npm run build && npm start`
- [ ] Deploy to Vercel with production environment variables
- [ ] Verify API base URL points to production backend
- [ ] Test end-to-end flows in production environment

---

## Success Criteria

- [ ] All customer pages implemented
- [ ] Fully responsive design
- [ ] API integration complete
- [ ] Error states handled gracefully
- [ ] Loading states for async operations
- [ ] Clean, accessible UI with TailwindCSS
- [ ] Deployed to Vercel and tested end-to-end

- [ ] Customer signup flow works
- [ ] Store search by postal code returns results
- [ ] Multi-item booking form validates input
- [ ] Booking creation shows price estimate
- [ ] Booking detail page displays status timeline
- [ ] Photos load with signed R2 URLs
- [ ] Notifications display and mark as read
- [ ] Navigation between pages works
- [ ] Responsive design on mobile

---

## Deployment

```bash
# Build and test locally
npm run build
npm run start

# Deploy to Vercel
vercel deploy --prod
```

**Environment Variables (Vercel):**

- Add all `.env.local` variables to Vercel dashboard
- Confirm API base URL points to production

---

## Success Criteria

- [ ] All customer pages implemented
- [ ] Fully responsive design
- [ ] API integration complete
- [ ] Error states handled gracefully
- [ ] Loading states for async operations
- [ ] Clean, accessible UI with TailwindCSS
- [ ] Deployed to Vercel and tested end-to-end

---

## Handoff to Module C

**What Module C needs from you:**

- Navigation component structure (can reuse pattern)
- API client utilities
- Shared UI components (Button, Input, Card, etc.)
- Confirmation that Clerk auth + routing works

**No conflicts expected** since Module C owns different route groups: `(store)`, `(courier)`, `(admin)`.
