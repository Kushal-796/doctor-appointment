# 🩺 Doctor Appointment Booking System

A web-based application built with **React**, **React Big Calendar**, **Firebase Authentication**, and **Firestore Database**. Patients can log in, view doctors, choose from available free slots, and book appointments. Doctors can log in to manage their availability and view confirmed bookings — all synced in real-time.

---

## 🚀 Tech Stack

* **Frontend**: React, React Big Calendar, TailwindCSS (for styling)
* **Backend**: Firebase Firestore (real-time database)
* **Authentication**: Firebase Auth (Email/Password)
* **Hosting**: Firebase Hosting (optional)

---

## ⚙️ Features

* 🔐 **Role-based Login** (Doctor & Patient)
* 👩‍⚕️ **Doctor Profiles** with specialization
* 📅 **Availability Slots** (Doctor sets free slots)
* ⏰ **Appointment Booking** (Patients book from free slots)
* 🔄 **Real-Time Sync** across patient and doctor dashboards
* 📊 **React Big Calendar Integration** for both sides
* 🛑 **Firestore Security Rules** for safe access control

---

## 📂 Firestore Database Structure

### `users` Collection

Stores all registered users (doctors & patients).

```json
{
  "uid": "123",
  "name": "Dr. John Smith",
  "email": "john@example.com",
  "role": "doctor", // "doctor" | "patient"
  "specialization": "Cardiology",
  "profilePic": "https://...",
  "createdAt": "2025-09-10T10:00:00"
}
```

### `availability` Collection

Stores doctors’ available time slots.

```json
{
  "doctorId": "123",
  "start": "2025-09-12T09:00:00",
  "end": "2025-09-12T09:30:00",
  "isBooked": false,
  "createdAt": "2025-09-10T08:00:00"
}
```

### `appointments` Collection

Stores confirmed bookings.

```json
{
  "appointmentId": "abc123",
  "doctorId": "123",
  "patientId": "456",
  "start": "2025-09-12T09:00:00",
  "end": "2025-09-12T09:30:00",
  "status": "confirmed",
  "createdAt": "2025-09-10T09:15:00"
}
```

### `notifications` Collection (Optional)

```json
{
  "userId": "456",
  "message": "Your appointment with Dr. John is confirmed.",
  "read": false,
  "createdAt": "2025-09-10T10:05:00"
}
```

---

## 🔄 User Flow

### 👩‍💻 Patient

1. Login via Firebase Auth (role: `patient`).
2. View **Doctor List Page** (all doctors from `users` where role=doctor).
3. Click on a doctor → open **Doctor Profile Page**.
4. See free slots from `availability` (`isBooked=false`).
5. Select slot → Firestore **transaction** creates an `appointment` and updates slot `isBooked=true`.
6. Appointment appears in **My Bookings**.

### 👨‍⚕️ Doctor

1. Login via Firebase Auth (role: `doctor`).
2. Set availability slots (stored in `availability`).
3. Open **Doctor Dashboard** → calendar view with all slots.
4. Booked slots update **in real-time** when patients book.
5. Doctor can view/manage confirmed appointments.

---

## 📊 Firestore Security Rules (Sample)

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Users: Only user can update own profile
    match /users/{userId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && request.auth.uid == userId;
    }

    // Availability: only doctors manage their slots
    match /availability/{slot} {
      allow read: if request.auth != null;
      allow write: if request.auth.token.role == "doctor" &&
                   request.auth.uid == request.resource.data.doctorId;
    }

    // Appointments: patients create, doctors update
    match /appointments/{id} {
      allow read: if request.auth != null;
      allow create: if request.auth.token.role == "patient";
      allow update: if request.auth.token.role == "doctor";
    }
  }
}
```

---

## 🖼️ UI Pages

* **Login Page** → Firebase Auth login/signup
* **Doctor List Page** → cards/grid of doctors
* **Doctor Profile Page** → doctor info + free slots
* **Booking Page** → slot confirmation + Firestore transaction
* **Patient Dashboard** → list of booked appointments
* **Doctor Dashboard** → React Big Calendar showing free/booked slots

---

## 🚀 Installation

```bash
# 1. Clone the repo
git clone https://github.com/your-username/doctor-appointment-system.git
cd doctor-appointment-system

# 2. Install dependencies
npm install

# 3. Setup Firebase
# - Enable Auth (Email/Password)
# - Create Firestore DB with collections: users, availability, appointments
# - Add Firebase config to project

# 4. Run locally
npm start
```

---

## 📌 Future Improvements

* Payment integration (Stripe/Razorpay)
* Google Calendar/Outlook sync (via APIs)
* Notifications (email/SMS reminders)
* Video consultations (Zoom/Meet API)

---

## ✨ Credits

Built with ❤️ using **React + Firebase**.
