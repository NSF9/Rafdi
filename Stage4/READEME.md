# رفدي — Rafdi Platform
### منصة تأجير المستودعات اللوجستية | Warehouse Rental Marketplace
 
[![Live](https://img.shields.io/badge/Live-rafdi.com-1a3a5c)](https://www.rafdi.com)
[![API](https://img.shields.io/badge/API-api.rafdi.com-1a3a5c)](https://api.rafdi.com/docs)
[![Backend](https://img.shields.io/badge/Backend-FastAPI-009688)](https://fastapi.tiangolo.com)
[![Database](https://img.shields.io/badge/Database-MySQL-4479A1)](https://www.mysql.com)
[![Deployment](https://img.shields.io/badge/Deployed-Railway-0B0D0E)](https://railway.app)
 
---
 
## 📌 Project Overview
 
Rafdi is a B2B warehouse rental marketplace built for the Saudi market. It connects companies that need storage space (renters) with companies that own warehouses (owners), with a platform intermediary taking commission from both sides.
 
| Role | Description |
|------|-------------|
| Warehouse Owner | Lists and manages warehouse spaces |
| Renting Company | Discovers, books, and pays for storage |
| Platform Admin | Oversees all operations and statistics |
 
---
 
## 👥 Team
 
| Name | Role | Responsibility |
|------|------|----------------|
| نواف صالح الزهراني | Team Lead / Backend | Architecture, Backend, DevOps |
| محمد فودة | Backend Developer | Backend APIs, Database |
| عون القرني | Frontend Developer | React UI, Integration |
| ياسر الشبانه | Frontend Developer | React UI, Components |
 
---
 
## 🏗️ Architecture
 
```
3-Tier Architecture
├── Tier 1 — Presentation (React + Tailwind)
│   └── www.rafdi.com
├── Tier 2 — Business Logic (FastAPI + Python)
│   └── api.rafdi.com
└── Tier 3 — Data (MySQL)
    └── Railway MySQL
```
 
### Design Patterns Applied
 
| Pattern | Implementation |
|---------|----------------|
| **SOLID Principles** | Every class has single responsibility, depends on abstractions |
| **Repository Pattern** | Separates DB logic from business logic |
| **Dependency Injection** | FastAPI `Depends()` injects repos into services |
| **Barrel Pattern** | `__init__.py` re-exports for clean imports |
| **Soft Delete** | `Status=False` / `IsActive=False` instead of DELETE |
| **Transaction Management** | `flush()` + `commit()` / `rollback()` |
 
---
 
## 🗂️ Project Structure
 
```
app/
├── api/                    # Route handlers (endpoints)
│   ├── Auth_Api.py
│   ├── Warehouse_Api.py
│   ├── Booking_Api.py
│   ├── Payment_Api.py
│   ├── Notification_Api.py
│   ├── Admin_Api.py
│   └── Auth_middleware.py  # JWT protection
├── models/                 # SQLAlchemy ORM models
│   ├── Base_Model.py       # Base + TimestampMixin
│   ├── User_Model.py
│   ├── Company_Model.py
│   ├── Role_Model.py
│   ├── User_Role_Model.py
│   ├── Warehouse_Model.py
│   ├── Booking_Model.py
│   ├── Payment_Model.py
│   └── Notification_Model.py
├── Dtos/                   # Pydantic schemas
│   ├── Auth_DTOs.py
│   ├── User_DTOs.py
│   ├── Company_DTOs.py
│   ├── Warehouse_DTOs.py
│   ├── Booking_DTOs.py
│   ├── Payment_DTOs.py
│   └── Notification_DTOs.py
├── Repo/                   # Database access layer
│   ├── Base_Repo.py        # Generic[T] abstract base
│   ├── user_repo.py
│   ├── Companey_Repo.py
│   ├── WarehouseRepo.py
│   ├── Booking_Repo.py
│   ├── Payment_Repo.py
│   ├── Notification_Repo.py
│   ├── Role_Repo.py
│   └── UserRoleRepo.py
├── services/               # Business logic layer
│   ├── User_services/
│   │   ├── auth_service.py
│   │   ├── password_service.py
│   │   ├── validation_service.py
│   │   ├── role_assignment_service.py
│   │   ├── otp_service.py
│   │   ├── email_service.py
│   │   └── forgot_password_service.py
│   ├── Booking_Services/
│   │   ├── booking_service.py
│   │   ├── BookingOverlap_Service.py
│   │   └── BookingPrice_Service.py
│   ├── Payment_Services/
│   │   ├── payment_service.py
│   │   └── commission_service.py
│   ├── Warehouse_services/
│   │   ├── warehouse_service.py
│   │   └── warehouse_access_service.py
│   ├── Jwt_Services/
│   │   └── jwt_service.py
│   ├── notification_service.py
│   └── notification_trigger_service.py
├── Enums/
│   └── EnumTypes.py
├── config.py               # DB connection
└── main.py                 # FastAPI app entry point
```
 
---
 
## 🔑 Key Technical Decisions
 
### 1. SOLID Principles
Every responsibility is isolated in its own class:
- `PasswordService` → hashing only
- `CommissionService` → 5% + 7% calculation only
- `BookingOverlapService` → date conflict detection only
- `ValidationService` → input validation only
### 2. JWT Authentication
Token payload contains `user_id`, `company_id`, and `roles` — no DB lookup needed per request.
 
```json
{
  "user_id": 17,
  "company_id": 18,
  "roles": ["warehouse_owner", "renter_company"],
  "exp": 1778447552
}
```
 
### 3. Transaction Management
Critical operations use `flush()` + single `commit()`:
 
```
Register:  Company → flush | User → flush | Role → flush | commit
Booking:   Booking → flush | Payment → flush | Notification → flush | commit
Payment:   Payment.Status=paid → flush | Booking.Status=confirmed → flush | commit
```
 
### 4. Commission Model
```
Renter pays:  Base Price + 5%
Owner receives: Base Price - 7%
Rafdi earns:  5% + 7% = 12% per booking
```
 
### 5. Dual Role Support
One company can be both owner and renter simultaneously via `user_roles` junction table.
 
---
 
## 🗄️ Database Schema
 
```
roles ──────────────── user_roles ──────── users
                                               │
companies ─────────────────────────────────────┘
    │
    ├── warehouses
    │       │
    │       └── bookings ──── payments
    │
    └── bookings (as renter)
 
users ──── notifications
```
 
**8 Tables:** `roles`, `companies`, `users`, `user_roles`, `warehouses`, `bookings`, `payments`, `notifications`
 
---
 
## 🌐 API Endpoints (22 total)
 
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/auth/register` | Public | Register company + user + roles |
| POST | `/auth/login` | Public | Login → JWT token |
| PATCH | `/auth/profile/email` | Any | Update email |
| PATCH | `/auth/profile/company` | Any | Update company name |
| POST | `/auth/forgot-password` | Public | Send OTP via email |
| POST | `/auth/reset-password` | Public | Reset password with OTP |
| GET | `/warehouses/` | Any | Get active warehouses |
| GET | `/warehouses/{id}` | Any | Get warehouse details |
| POST | `/warehouses/` | Owner | Create warehouse |
| PATCH | `/warehouses/{id}` | Owner | Update warehouse |
| PATCH | `/warehouses/{id}/toggle` | Owner | Toggle IsActive |
| POST | `/bookings/` | Renter | Create booking + auto payment |
| GET | `/bookings/my` | Any | Get my bookings |
| PATCH | `/bookings/{id}/status` | Any | Update booking status |
| POST | `/payments/{booking_id}` | Renter | Process payment |
| GET | `/payments/{booking_id}` | Any | Get payment + commission |
| GET | `/notifications/` | Any | Get notifications |
| PATCH | `/notifications/{id}/read` | Any | Mark as read |
| PATCH | `/notifications/read-all` | Any | Mark all as read |
| GET | `/admin/companies` | Admin | All companies |
| PATCH | `/admin/companies/{id}/status` | Admin | Enable/disable company |
| GET | `/admin/warehouses` | Admin | All warehouses |
| GET | `/admin/bookings` | Admin | All bookings |
| GET | `/admin/dashboard` | Admin | Platform statistics |
 
---
 
## 🚀 Sprint Plan
 
### Sprint 1 — Setup + Models + DTOs
**21 Feb → 6 Mar 2026**
 
| Task | Assignee | Status |
|------|----------|--------|
| Project setup — FastAPI + Docker + Railway | نواف | ✅ Done |
| Database schema design (8 tables) | نواف + محمد | ✅ Done |
| SQLAlchemy v2 Models + TimestampMixin | نواف + محمد | ✅ Done |
| Pydantic DTOs — all models | نواف + محمد | ✅ Done |
| Base Repository — Generic[T] | نواف | ✅ Done |
| React project setup + Tailwind | عون + ياسر | ✅ Done |
 
---
 
### Sprint 2 — Auth + JWT + Warehouse
**7 Mar → 20 Mar 2026**
 
| Task | Assignee | Status |
|------|----------|--------|
| All Repositories (8 repos) | محمد | ✅ Done |
| AuthService — register + login | نواف | ✅ Done |
| JWT Service + auth middleware | نواف | ✅ Done |
| Role assignment + dual-role support | نواف | ✅ Done |
| PasswordService — PBKDF2 via cryptography | نواف | ✅ Done |
| WarehouseService + endpoints | محمد | ✅ Done |
| Login / Register screens | عون + ياسر | ✅ Done |
| Warehouse listing page | عون + ياسر | ✅ Done |
 
---
 
### Sprint 3 — Booking + Payment
**21 Mar → 3 Apr 2026**
 
| Task | Assignee | Status |
|------|----------|--------|
| BookingOverlapService | محمد | ✅ Done |
| BookingPriceService | محمد | ✅ Done |
| BookingService + auto payment creation | محمد | ✅ Done |
| CommissionService — 5% + 7% | نواف | ✅ Done |
| PaymentService — confirm booking on pay | نواف | ✅ Done |
| Booking + Payment endpoints | نواف + محمد | ✅ Done |
| Booking form + payment screen | عون + ياسر | ✅ Done |
| My bookings dashboard | عون + ياسر | ✅ Done |
 
---
 
### Sprint 4 — Notifications + Admin + Profile
**4 Apr → 17 Apr 2026**
 
| Task | Assignee | Status |
|------|----------|--------|
| NotificationService | نواف | ✅ Done |
| NotificationTriggerService | نواف | ✅ Done |
| CompanyService + AdminService | محمد | ✅ Done |
| UserProfileService + CompanyProfileService | محمد | ✅ Done |
| Admin endpoints (companies, warehouses, bookings, dashboard) | نواف + محمد | ✅ Done |
| Notification bell + dropdown | عون + ياسر | ✅ Done |
| Admin dashboard screens | عون + ياسر | ✅ Done |
| Profile settings screen | عون + ياسر | ✅ Done |
 
---
 
### Sprint 5 — OTP + Integration + CORS
**18 Apr → 1 May 2026**
 
| Task | Assignee | Status |
|------|----------|--------|
| OTPService + EmailService (SendGrid) | نواف | ✅ Done |
| ForgotPasswordService + endpoints | نواف | ✅ Done |
| CORS configuration | نواف | ✅ Done |
| Frontend ↔ Backend integration | عون + ياسر | ✅ Done |
| Moyasar payment gateway integration | نواف + محمد | ✅ Done |
| End-to-end flow testing | الفريق | ✅ Done |
 
---
 
### Sprint 6 — Testing + Deployment + QA
**2 May → 18 Jun 2026**
 
| Task | Assignee | Status |
|------|----------|--------|
| Pytest unit tests | محمد | ✅ Done |
| Smoke testing — 10 critical flows | ياسر | ✅ Done |
| Docker production configuration | نواف | ✅ Done |
| Railway deployment — api.rafdi.com | نواف | ✅ Done |
| DNS setup — rafdi.com + api.rafdi.com | نواف | ✅ Done |
| MySQL constraints + FK setup | محمد | ✅ Done |
| Bug fixes + performance review | الفريق | ✅ Done |
| Final QA sign-off | ياسر | ✅ Done |
 
---
 
## 📋 Sprint Retrospectives
 
### Sprint 1 Retrospective
**✅ Went well:**
- TimestampMixin reduced code repetition across all 8 models
- SQLAlchemy v2 `Mapped` syntax gave clean type safety
**❌ Challenges:**
- Circular imports between models took significant time to resolve
- Windows vs Linux case-sensitivity caused deployment issues
**🔧 Improvements:**
- Use `TYPE_CHECKING` from the first line in every model file
- Agree on file naming convention (PascalCase) before coding
---
 
### Sprint 2 Retrospective
**✅ Went well:**
- SOLID separation made each service easy to test independently
- JWT payload design eliminated extra DB queries per request
**❌ Challenges:**
- Dependency injection setup took time to understand
- Railway environment variables caused DATABASE_URL issues
**🔧 Improvements:**
- Document DI pattern for the team
- Add `.env` validation at startup
---
 
### Sprint 3 Retrospective
**✅ Went well:**
- Transaction management worked correctly — rollback tested and verified
- Auto-payment creation on booking simplified the frontend flow
**❌ Challenges:**
- Overlap check logic edge cases (adjacent dates) needed extra care
- Commission calculation required clarification on base price vs total
**🔧 Improvements:**
- Write unit tests for overlap immediately, not after
- Document commission model clearly for frontend team
---
 
### Sprint 4 Retrospective
**✅ Went well:**
- Notification trigger service decoupled events from notifications cleanly
- Admin dashboard came together faster than expected
**❌ Challenges:**
- N+1 query problem discovered late in warehouse listings
- Profile update split into two endpoints caused frontend coordination overhead
**🔧 Improvements:**
- Add `joinedload` to all list endpoints from the start
- Discuss API contract with frontend before implementing
---
 
### Sprint 5 Retrospective
**✅ Went well:**
- OTP flow with SendGrid worked on first attempt
- CORS configuration resolved frontend connection issues immediately
**❌ Challenges:**
- Moyasar 3DS redirect handling required extra coordination
- Frontend-backend field naming mismatches caused integration delays
**🔧 Improvements:**
- Share DTOs / API contract document with frontend team earlier
- Test CORS locally before deploying
---
 
### Sprint 6 Retrospective
**✅ Went well:**
- Full deployment pipeline (Docker → Railway) worked smoothly
- Smoke testing caught 3 bugs before final submission
**❌ Challenges:**
- MySQL Foreign Key constraints were missing initially due to ORM table creation
- Procfile vs Dockerfile command conflict caused early deployment failures
**🔧 Improvements:**
- Always run `SHOW CREATE TABLE` after migration to verify FK constraints
- Add health check endpoint for deployment verification
---
 
## 🧪 QA Strategy
 
### Swagger UI Testing
All 22 endpoints tested via `https://api.rafdi.com/docs`
 
### Smoke Testing Checklist
 
| # | Flow | Role | Expected |
|---|------|------|----------|
| 1 | Register as Owner | Owner | Account created ✅ |
| 2 | Login | Owner | JWT issued ✅ |
| 3 | Add warehouse | Owner | Appears in listing ✅ |
| 4 | Register as Renter | Renter | Account created ✅ |
| 5 | Browse warehouses | Renter | Active warehouses shown ✅ |
| 6 | Book warehouse | Renter | Booking + pending payment created ✅ |
| 7 | Book same dates | Renter | 409 conflict returned ✅ |
| 8 | Process payment | Renter | Payment paid + booking confirmed ✅ |
| 9 | Check notifications | Owner + Renter | Correct alerts received ✅ |
| 10 | Admin disable company | Admin | Status set to false ✅ |
 
### Unit Tests (Pytest)
Critical functions tested:
 
| Function | Test Cases |
|----------|-----------|
| `calculateCommission()` | 5% renter, 7% owner, correct totals |
| `checkOverlap()` | Overlapping rejected, non-overlapping accepted |
| `hashPassword()` | Returns hash not plaintext, unique salts |
| `verifyPassword()` | Correct → True, Wrong → False |
| `toggleActive()` | True→False, False→True |
 
---
 
## ⚙️ Tech Stack
 
| Layer | Technology |
|-------|-----------|
| Backend | FastAPI (Python 3.11) |
| ORM | SQLAlchemy v2 |
| Database | MySQL 8 |
| Authentication | JWT via python-jose |
| Password Hashing | PBKDF2 via cryptography |
| Email | SendGrid |
| Payment | Moyasar |
| Containerization | Docker + docker-compose |
| Deployment | Railway |
| Frontend | React + Vite + Tailwind CSS |
 
---
 
## 🔧 Local Development
 
```bash
# Clone the repo
git clone https://github.com/your-repo/rafdi-platform.git
cd rafdi-platform
 
# Setup environment
cp .env.example .env
# Edit .env with your values
 
# Run with Docker
docker-compose up --build
 
# API docs
open http://localhost:8000/docs
```
 
### Environment Variables
 
```env
DATABASE_URL=mysql+pymysql://root:password@db:3306/rafdi
SECRET_KEY=your-secret-key
SENDGRID_API_KEY=SG.xxxxxxxxxx
FROM_EMAIL=noreply@rafdi.com
ADMIN_EMAIL=admin@rafdi.com
ADMIN_PASSWORD=your-admin-password
MOYASAR_API_KEY=your-moyasar-key
```
 
---
 
## 🌍 Deployment
 
| Service | URL |
|---------|-----|
| Frontend | https://www.rafdi.com |
| Backend API | https://api.rafdi.com |
| API Docs | https://api.rafdi.com/docs |
| Platform | Railway |
 
---
 
## 📁 SCM Strategy
 
```
main          ← Production only. Never commit directly.
development   ← All features merge here via PR.
feature/*     ← Individual features. e.g. feature/booking-overlap
```
 
### Commit Convention
```
feat:     feat: add date overlap validation
fix:      fix: correct commission calculation
refactor: refactor: split UserService into AuthService
docs:     docs: update API reference
test:     test: add pytest for calculateCommission
```
 
---
 
*Rafdi Platform — MVP v1.0 | 2026*