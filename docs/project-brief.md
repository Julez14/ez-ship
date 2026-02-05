# PROJECT BRIEF (Riipen / Student Developers)

## MVP: Uber-Style Storage Platform Using Convenience Stores as Micro-Warehouses

## 1. Project Title

**"On-Demand Storage & Last-Mile Pickup Platform (Uber for Storage)"**

---

## 2. Project Description (Clear, Sharable Summary)

We are developing the MVP of a **new logistics platform that works like Uber, but for temporary storage**.

**How it works:**

- **Individuals and small businesses** drop off items at **nearby convenience stores**, which act as **micro-warehouses**.
- Stores check in and hold the items for a short time (hours or days).
- **Last-mile delivery companies** (Uber-style couriers, local delivery services) pick up stored items and deliver them to final destinations.

The MVP will demonstrate the core flow:  
**Booking → Drop-off at store → Storage → Pickup by last-mile partner → Delivery confirmation**

This student-built MVP will support real-world pilot testing.

---

## 3. MVP Objectives

Students will build a web application that allows:

**For Customers:**

- Search for nearby convenience stores
- Book storage time
- Drop off items
- Get notifications when the item is picked up or delivered

**For Convenience Stores:**

- Log in as a partner
- Check in items (size, photo, timestamp)
- Store items in inventory
- Handover items to delivery couriers
- Track status changes

**For Last-Mile Delivery Partners:**

- Accept pickup tasks
- Mark pickups and deliveries as complete
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
