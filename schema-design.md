# Smart Clinic Management System — Schema Design

---

## MySQL Database Design

### Table: patients
- id: INT, Primary Key, Auto Increment
- first_name: VARCHAR(50), NOT NULL
- last_name: VARCHAR(50), NOT NULL
- email: VARCHAR(100), UNIQUE, NOT NULL
- phone: VARCHAR(15), UNIQUE, NOT NULL
- date_of_birth: DATE, NOT NULL
- gender: ENUM('Male', 'Female', 'Other'), NOT NULL
- address: VARCHAR(255), NULL
- created_at: DATETIME, NOT NULL, default CURRENT_TIMESTAMP
- updated_at: DATETIME, NOT NULL, default CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP

> **Notes:** Patients are core to the system; emails and phones must be unique. If a patient is deleted, appointments should ideally remain for record keeping but be marked inactive or patient_id set to NULL with a soft-delete approach.

---

### Table: doctors
- id: INT, Primary Key, Auto Increment
- first_name: VARCHAR(50), NOT NULL
- last_name: VARCHAR(50), NOT NULL
- email: VARCHAR(100), UNIQUE, NOT NULL
- phone: VARCHAR(15), UNIQUE, NOT NULL
- specialization: VARCHAR(100), NOT NULL
- available_from: TIME, NOT NULL
- available_to: TIME, NOT NULL
- created_at: DATETIME, NOT NULL, default CURRENT_TIMESTAMP
- updated_at: DATETIME, NOT NULL, default CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP

> **Notes:** Doctors have fixed daily available hours for scheduling appointments. Overlapping appointments for the same doctor should be prevented by business logic in application layer.

---

### Table: appointments
- id: INT, Primary Key, Auto Increment
- doctor_id: INT, Foreign Key → doctors(id), NOT NULL
- patient_id: INT, Foreign Key → patients(id), NOT NULL
- appointment_time: DATETIME, NOT NULL
- status: ENUM('Scheduled', 'Completed', 'Cancelled'), NOT NULL, default 'Scheduled'
- created_at: DATETIME, NOT NULL, default CURRENT_TIMESTAMP
- updated_at: DATETIME, NOT NULL, default CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP

> **Notes:** Appointment status controls lifecycle. Appointment time must be within doctor's available hours. If a patient or doctor is deleted, appointments remain for audit but status can be updated to 'Cancelled'.

---

### Table: admins
- id: INT, Primary Key, Auto Increment
- username: VARCHAR(50), UNIQUE, NOT NULL
- password_hash: VARCHAR(255), NOT NULL
- email: VARCHAR(100), UNIQUE, NOT NULL
- role: ENUM('Admin', 'Receptionist', 'Manager'), NOT NULL
- created_at: DATETIME, NOT NULL, default CURRENT_TIMESTAMP
- updated_at: DATETIME, NOT NULL, default CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP

> **Notes:** Admins manage the system, roles define permissions. Passwords are stored securely as hashes.

---

### Table: payments
- id: INT, Primary Key, Auto Increment
- appointment_id: INT, Foreign Key → appointments(id), NOT NULL
- amount: DECIMAL(10, 2), NOT NULL
- payment_method: ENUM('Cash', 'Credit Card', 'Insurance', 'Online'), NOT NULL
- payment_date: DATETIME, NOT NULL, default CURRENT_TIMESTAMP
- status: ENUM('Pending', 'Completed', 'Failed'), NOT NULL, default 'Pending'

> **Notes:** Payments tied to appointments. Useful for billing and audit.

---

## MongoDB Collection Design

### Collection: prescriptions

```json
{
  "_id": "ObjectId('64abc1234567890abcdef1234')",
  "appointmentId": 101,
  "patientId": 55,
  "doctorId": 10,
  "medications": [
    {
      "name": "Paracetamol",
      "dosage": "500mg",
      "frequency": "Every 6 hours",
      "duration": "5 days"
    },
    {
      "name": "Ibuprofen",
      "dosage": "200mg",
      "frequency": "Every 8 hours",
      "duration": "3 days"
    }
  ],
  "doctorNotes": "Take medications after meals. Avoid alcohol.",
  "refillCount": 1,
  "pharmacy": {
    "name": "City Pharmacy",
    "address": "123 Main St, Springfield"
  },
  "issuedDate": "2025-08-10T09:30:00Z",
  "tags": ["pain relief", "anti-inflammatory"],
  "attachments": [
    {
      "filename": "lab_report_2025_08_09.pdf",
      "url": "https://clinic-files.example.com/reports/lab_report_2025_08_09.pdf"
    }
  ]
}
