# Study Hall

A simple mobile application for study-hall operators to manage **cabinet availability** through a visual, seat-map-style interface.

The application allows an operator to configure their study-hall floors/areas, define cabinet names based on the physical layout, and quickly block or release cabinets from a visual dashboard.

The goal is to build a **small, polished, practical MVP** without introducing unnecessary ERP-style complexity.

---

## 🎯 Project Goal

Study Hall solves one simple problem:

> **Help a study-hall operator quickly see which cabinets are available and which are blocked, and change their status when required.**

The main dashboard is designed around the mental model of:

* Bus seat selection
* Movie ticket seat selection
* Visual floor/cabinet maps

The application should feel like a **modern mobile utility**, not a traditional administration/CRUD application.

---

# 🚀 MVP Scope

The operator can:

1. Register
2. Login
3. Create Floor/Area names
4. Add cabinets under each Floor/Area
5. View all cabinets visually
6. See Available and Blocked cabinets
7. Filter cabinets
8. Block an Available cabinet
9. Release a Blocked cabinet
10. Logout

---

# ❌ Explicitly Out of Scope

To keep the MVP small and finishable, the following are intentionally **not included**:

* Student/customer records
* Student login
* Cabinet booking
* Booking history
* Occupancy history
* Payment tracking
* Fee management
* Duration tracking
* Attendance
* Student profiles
* Reports
* Analytics
* Complex roles and permissions
* Multi-tenant architecture
* Notifications
* Subscription management
* Business/accounting features

The application is **only about cabinet availability management**.

---

# 🧑‍💼 User

The application user is the **study-hall operator/admin**.

The operator manages the physical cabinets in their study hall.

The application does not maintain information about the people using those cabinets.

---

# 🏢 Flexible Floor / Area Structure

Different study halls may have completely different physical layouts.

Therefore, the application does not force names such as:

* Ground Floor
* First Floor
* Second Floor

Instead, the operator can enter whatever names match their physical study hall.

Examples:

```text
1.5 Floor
2nd Floor
Block A
Block B
Room 1
Reading Hall
North Wing
```

Similarly, cabinet names are completely flexible:

```text
A1
A2
101
102
VIP-1
VIP-2
C-01
```

The operator defines the names during setup.

---

# 📱 Application Flow

```text
Register
   ↓
Login
   ↓
Floor / Area Setup
   ↓
Cabinet Setup
   ↓
Dashboard
   ↓
Select Cabinet
   ↓
Block / Release
```

---

# 📱 Screens

## 1. Splash / Loading

A minimal branded loading screen while the application restores the authentication session.

---

## 2. Login

The operator enters:

* Email
* Password

Actions:

* Login
* Go to Register

API:

```text
POST /api/v1/auth/login
```

---

## 3. Register

The operator enters:

* Name
* Email
* Password
* Confirm Password

API:

```text
POST /api/v1/auth/register
```

---

## 4. Floor / Area Setup

The operator creates the areas used by their study hall.

Example:

```text
1.5 Floor
2nd Floor
```

API:

```text
POST /api/v1/floors
```

---

## 5. Cabinet Setup

The operator selects a Floor/Area and adds cabinet names.

Example:

```text
A1
A2
A3
A4
A5
```

Multiple cabinets can be created in one API request.

API:

```text
POST /api/v1/floors/:floorId/cabinets
```

New cabinets automatically start as:

```text
available
```

---

# ⭐ 6. Dashboard

The Dashboard is the **hero screen of the application**.

It should immediately show:

### Operator

```text
Welcome, Ravi
```

### Summary

```text
Total       Available       Blocked
 42            28              14
```

### Floor / Area sections

```text
1.5 Floor

┌────┐ ┌────┐ ┌────┐ ┌────┐
│ A1 │ │ A2 │ │ A3 │ │ A4 │
└────┘ └────┘ └────┘ └────┘

2nd Floor

┌────┐ ┌────┐ ┌────┐
│ B1 │ │ B2 │ │ B3 │
└────┘ └────┘ └────┘
```

The cabinet grid should visually communicate availability immediately.

---

# 🔎 Cabinet Filters

The dashboard provides client-side filters:

```text
All
Available
Blocked
```

No separate API endpoints are required for filtering.

Filtering happens using the dashboard data already loaded into the application.

---

# 🔄 Cabinet Status

A cabinet has only two states:

```text
available
blocked
```

### Available cabinet

Selecting it provides:

```text
Block Cabinet
```

### Blocked cabinet

Selecting it provides:

```text
Release Cabinet
```

API:

```text
PATCH /api/v1/cabinets/:cabinetId/status
```

Request:

```json
{
  "status": "blocked"
}
```

or:

```json
{
  "status": "available"
}
```

---

# 🎨 UI Direction

The application should have a **premium mobile-first visual identity**.

The dashboard should not look like a conventional admin panel.

### Design principles

* Rounded cards
* Clean typography
* Generous spacing
* Strong visual hierarchy
* Subtle shadows
* Premium filter chips
* Clear Available/Blocked states
* Visual cabinet grid
* Smooth but restrained interactions
* Mobile-first layout

The visual inspiration is closer to:

```text
Movie Seat Selection
        +
Modern Mobile Dashboard
        +
Physical Study Hall Layout
```

rather than:

```text
Traditional CRUD Admin Panel
```

---

# 🛠 Technology Stack

## Mobile Frontend

* React Native
* Expo
* TypeScript
* Expo Router
* Tamagui
* React Context API
* Expo SecureStore

State management:

```text
React Context
```

Redux is intentionally not used.

---

## Backend

* Node.js
* Express
* TypeScript
* Mongoose
* JWT
* bcryptjs
* CORS
* dotenv

---

## Database

MongoDB Atlas

Using:

```text
Mongoose
```

---

# 🏗 Architecture

```text
┌──────────────────────────┐
│      React Native        │
│          Expo            │
│                          │
│     Tamagui + Context    │
└────────────┬─────────────┘
             │
             │ HTTPS REST API
             ▼
┌──────────────────────────┐
│     Node.js / Express    │
│        TypeScript        │
│                          │
│       JWT Auth           │
└────────────┬─────────────┘
             │
             │ Mongoose
             ▼
┌──────────────────────────┐
│       MongoDB Atlas      │
└──────────────────────────┘
```

---

# 🔌 API Endpoints

The MVP intentionally contains only **six main endpoints**.

## Authentication

### Register

```http
POST /api/v1/auth/register
```

Request:

```json
{
  "name": "Ravi Kumar",
  "email": "ravi@example.com",
  "password": "Password@123"
}
```

---

### Login

```http
POST /api/v1/auth/login
```

Request:

```json
{
  "email": "ravi@example.com",
  "password": "Password@123"
}
```

Response contains:

* JWT token
* User ID
* User name
* User email

---

# Dashboard

### Get Dashboard

```http
GET /api/v1/dashboard
```

Returns:

* Operator
* Summary
* Floors
* Cabinets
* Cabinet status

Example structure:

```json
{
  "success": true,
  "data": {
    "operator": {
      "id": "68d2...",
      "name": "Ravi Kumar"
    },
    "summary": {
      "total": 42,
      "available": 28,
      "blocked": 14
    },
    "floors": [
      {
        "id": "floor_001",
        "name": "1.5 Floor",
        "cabinets": [
          {
            "id": "cab_001",
            "name": "A1",
            "status": "available"
          },
          {
            "id": "cab_002",
            "name": "A2",
            "status": "blocked"
          }
        ]
      }
    ]
  }
}
```

---

# Floor Setup

### Create Floor / Area

```http
POST /api/v1/floors
```

Request:

```json
{
  "name": "1.5 Floor"
}
```

---

# Cabinet Setup

### Add Cabinets

```http
POST /api/v1/floors/:floorId/cabinets
```

Request:

```json
{
  "cabinets": [
    {
      "name": "A1"
    },
    {
      "name": "A2"
    },
    {
      "name": "
```
