<<<<<<< HEAD
# 🏥 MediTech – Smart Health Tracking System

Standalone web app — just open `index.html` in a browser. No backend, no install needed.

## 📁 Files
```
meditech/
├── index.html   → Full SPA (user app + admin app)
├── style.css    → Complete responsive stylesheet
├── script.js    → All app logic + localStorage
├── Jenkinsfile  → CI/CD pipeline
└── README.md
```

## 🔀 Role-Based Login
```
Login
 ├── admin  →  👑 Admin Dashboard  (purple sidebar)
 └── user   →  💙 User Dashboard   (blue sidebar)
```

## 🔑 Demo Accounts
| Role  | Email               | Password   |
|-------|---------------------|------------|
| User  | user@demo.com       | demo1234   |
| Admin | admin@demo.com      | admin1234  |

## 🚀 Run
```bash
# Double-click index.html  OR
python3 -m http.server 8080   # http://localhost:8080
```

## ✨ User Features
- Medicine CRUD with reminders & color coding
- Dashboard: today's schedule, mark as taken, adherence %
- Health Log: BP, sugar, weight, heart rate, mood, symptoms
- 7/14/30-day history with adherence analytics
- Doctor management with appointment dates
- Chart.js reports (BP, sugar, weight, heart rate)
- Profile edit & password change

## 👑 Admin Features
- Separate admin dashboard (admins never see user pages)
- System stats: total users, active users, medicines, logs
- All users table with search & activate/deactivate
- Missed doses tracker across all users

## 🔒 Privacy
Every user's data is stored under their own key:
`mt_{userId}_medicines`, `mt_{userId}_hlogs`, etc.
No user can access another user's data.
=======
# MediTrack-Smart-Health-Tracking-System
>>>>>>> cd3e235162f68f424dc0eb4665f3deeb3bf80b80
