# PROJECT BRIEF

## MVP: Last-Mile Delivery & Storage Platform Using Convenience Stores

## 1. Project Title

**"E-Z Ship: On-Demand Storage & Last-Mile Delivery Platform"**

---

## 2. Project Description

We are developing the **Minimum Viable Product (MVP)** of a **last-mile delivery and logistics platform** that enables temporary storage at convenience stores.

**How it works:**

- **Customers** book storage and drop off items at **participating convenience stores**, which act as **temporary storage locations**.
- Store partners check in and hold items securely.
- **Drivers** pick up stored items and deliver them to final destinations.

The MVP demonstrates the core workflow:  
**Booking → Drop-off at store → Storage → Pickup by driver → Delivery confirmation**

This MVP supports validation of the business concept and early stakeholder discussions.

**Demonstration Readiness:** The MVP must be strong enough for live conversations with potential partners, including convenience store owners, drivers, and logistics stakeholders. It should support clear, uninterrupted demonstrations without requiring hypothetical explanations or verbal walkthroughs of missing functionality.

---

## 3. MVP Objectives

Build a web application that demonstrates:

**For Customers:**

- Search for participating convenience stores by postal code
- Book storage time with item details
- Track shipment status through delivery
- View notifications for status changes

**For Store Partners (HIGH PRIORITY - Central to Business Model):**

- Exceptionally clear and intuitive partner dashboard
- Declare available storage space in square footage
- View items currently in storage
- Check in items with photo verification
- See when items are picked up or removed
- Simple enough to explain to store owners in under 2 minutes

**For Drivers:**

- View and accept delivery tasks
- Upload photos for pickup and delivery
- Track task completion
- Upload proof-of-delivery (photo/signature)

**For the Admin (You):**

- View all stores, users, items
- Monitor bookings
- Manage pricing rules
- Export data

---

## 4. Student Tasks (Clear & Structured)

### A. Product & UX Design

- Create user journeys (Customer → Store → Courier → Admin)
- Design wireframes in Figma for:
  - Customer booking interface
  - Partner store dashboard
  - Courier pickup dashboard
  - Admin panel

---

### B. Customer Side Development

- Store finder (map optional for MVP; list view acceptable)
- Booking form (size, duration, notes)
- Account or simple email verification
- Booking confirmation
- Status notifications

---

### C. Partner Store Dashboard

Stores must be able to:

- Log in
- Check in items
- Take/upload item photos
- Assign unique item IDs
- Track storage status
- Mark items "ready for pickup"
- Handover items to courier and confirm pickup

---

### D. Last-Mile Courier Dashboard

Couriers must be able to:

- Log in
- See available pickups
- Accept tasks
- Mark pickups as complete (with timestamp)
- Mark deliveries as complete (photo proof)

---

### E. Database & Item Tracking

Create database tables/collections for:

- Users
- Stores
- Couriers
- Items
- Bookings
- Transactions
- Status logs (each action is timestamped)

---

### F. Pricing & Billing Logic

- Price = size × duration × (optional) delivery fee
- Display estimated costs at booking
- Store revenue share calculations (simple %)

### G. Notifications System

Minimum:

- Email confirmations
- Email alerts to stores & couriers

Optional:

- WhatsApp links for manual messaging
- SMS via simple API

---

### H. Admin Dashboard

- Manage stores
- Manage bookings
- View item history
- Monitor courier performance
- Export CSV

---

## 5. Technical Scope (Recommended Stack)

**Frontend:**

- **Next.js (React)** -- fast, modern, scalable
- **TailwindCSS** -- improves student productivity

**Backend:**

- **Next.js API routes** or **Node.js + Express**

**Database:**

- **Firebase Firestore** (simplest)  
  or
- **MongoDB Atlas**

**Authentication:**

- Firebase Auth  
  or
- Clerk / NextAuth

**Hosting:**

- **Vercel** (fastest for student teams)

## 6. Deliverables

At project completion, students provide:

- Fully functional MVP website
- Figma UX/UI designs
- Source code in GitHub
- Technical documentation
- Deployment link
- Demo walkthrough video (5--10 minutes)

---

## 7. Suggested Timeline (8--12 Weeks)

**Weeks 1--2: Research & Figma**

**Weeks 3--4: Customer-facing features**

**Weeks 5--6: Store dashboard**

**Weeks 6--7: Courier dashboard**

**Weeks 8--9: Admin panel**

**Week 10: Integration & testing**

**Weeks 11--12: Deployment & demo**
