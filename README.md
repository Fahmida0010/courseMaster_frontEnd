# 🐛 CourseMaster Bug Reports & Fixes

Here is the detailed tracking report of all identified bugs, their severity, root causes, and resolutions for the **CourseMaster** project.

---

## 📊 Summary Table

| Bug ID | Title | Severity | Status |
| :--- | :--- | :--- | :--- |
| **BUG-001** | Login button does not respond upon clicking | `Critical (P1)` | `Fixed & Verified` |
| **BUG-002** | "No courses found" message displayed on Courses page | `High (P2)` | `Fixed & Verified` |
| **BUG-003** | Dashboard and Logout buttons missing from Navbar after login | `High (P2)` | `Fixed & Verified` |
| **BUG-004** | Student dashboard progress data not rendering on frontend | `High (P2)` | `Fixed & Verified` |
| **BUG-005** | Course card titles are not responsive on smaller screens | `Low (P4)` | `Fixed & Verified` |
| **BUG-006** | Auth state mismatch & invalid redirection after registration | `Medium (P3)` | `Fixed & Verified` |
| **BUG-007** | Auth middleware calls `next()` on JWT verification failure | `Critical (P1)` | `Fixed & Verified` |
| **BUG-008** | Hardcoded dev bypass tokens cause 401 Unauthorized errors | `High (P2)` | `Fixed & Verified` |
| **BUG-009** | Assignment submission filter queries non-existent `courseId` | `High (P2)` | `Fixed & Verified` |
| **BUG-010** | Registration payload omits `role` and `adminKey` fields | `High (P2)` | `Fixed & Verified` |
| **BUG-011** | Inverted conditional check in `enrollCourse` blocks enrollments | `Critical (P1)` | `Fixed & Verified` |
| **BUG-012** | Missing `await` keyword on `enrollment.save()` in `completeLesson` | `High (P2)` | `Fixed & Verified` |
| **BUG-013** | Incorrect quiz percentage calculation using 10 instead of 100 | `High (P2)` | `Fixed & Verified` |
| **BUG-014** | Unvalidated `adminKey` in Zod registration schema | `Medium (P3)` | `Fixed & Verified` |
| **BUG-015** | Restrictive CORS headers blocking `PUT`, `DELETE`, and `PATCH` | `High (P2)` | `Fixed & Verified` |

---

## 🔎 Detailed Bug Reports

### 🔴 BUG-001 — Login button does not respond upon clicking
* **Location & Steps to Reproduce:** Login page $\rightarrow$ Enter valid credentials $\rightarrow$ Click "Login".
* **Expected vs. Actual Result:** Expected the user to log in successfully, but nothing happens after clicking the button.
* **Severity & Evidence:** `Critical (P1)` — API Error Response: `{"message":"Invalid email or password"}`
* **Fix & Verification:** Corrected the login event handler and verified that the authentication flow functions properly.
* **Status:** `Fixed & Verified`

---

### 🟠 BUG-002 — "No courses found" message displayed on Courses page
* **Location & Steps to Reproduce:** Navigation Bar $\rightarrow$ Visit the Courses page.
* **Expected vs. Actual Result:** Expected the Courses page to display all available course data. Instead, no courses are displayed, and a "No courses data" message appears.
* **Severity & Evidence:** `High (P2)` — UI displays "No courses data".
* **Fix & Verification:** Seeded course data into the database and verified that all course cards are correctly showcased on the page.
* **Status:** `Fixed & Verified`

---

### 🟠 BUG-003 — Dashboard and Logout buttons missing from Navbar after login
* **Location & Steps to Reproduce:** Navbar $\rightarrow$ Log in to account $\rightarrow$ Visit the Home page.
* **Expected vs. Actual Result:** Expected the Navbar to update and display the "Dashboard" and "Logout" buttons upon successful login. Instead, the Navbar remains unchanged.
* **Severity & Evidence:** `High (P2)` — No console errors thrown, but the Navbar UI state fails to update post-authentication.
* **Fix & Verification:** Resolved the state synchronization issue in the Navbar component and verified that the "Dashboard" and "Logout" routes appear correctly upon logging in.
* **Status:** `Fixed & Verified`

---

### 🟠 BUG-004 — Student dashboard progress data not rendering on frontend
* **Location & Steps to Reproduce:** Student Dashboard $\rightarrow$ Navigate to Student overview page $\rightarrow$ Inspect `/me` and `/my` API responses in Network tab.
* **Expected vs. Actual Result:** Expected student progress metrics (completed lessons and progress percentages) to render accurately on the dashboard. Instead, the dashboard appears blank even though the API responses contain the necessary progress payload.
* **Severity & Evidence:** `High (P2)` — API response includes complete progress data, but the dashboard fails to render it.
* **Fix & Verification:** Fixed frontend data mapping and rendering logic to ensure student progress data is accurately extracted from the API response and displayed on the dashboard UI.
* **Status:** `Fixed & Verified`

---

### 🟢 BUG-005 — Course card titles are not responsive on smaller screens
* **Location & Steps to Reproduce:** Public Courses Page $\rightarrow$ View course list on varying screen sizes.
* **Expected vs. Actual Result:** Expected course titles inside card components to wrap and scale responsively across all devices. Instead, titles overflow or truncate incorrectly on smaller viewports.
* **Severity & Evidence:** `Low (P4)` — Text overflow and layout breaking issues observed on course card titles.
* **Fix & Verification:** Applied responsive Tailwind utility classes (`line-clamp-2`) to course title elements and verified consistent layout rendering across mobile, tablet, and desktop viewports.
* **Status:** `Fixed & Verified`

---

### 🟡 BUG-006 — Auth state mismatch & invalid redirection after registration
* **Location & Steps to Reproduce:** Registration page (`/register`) $\rightarrow$ Enter valid Name, Email, and Password $\rightarrow$ Click "Create Account".
* **Expected vs. Actual Result:** Expected the user to be automatically authenticated and redirected to the home page (`/`) with the navbar updated. Actual: Initially redirected to login page or home page without updating the Redux auth state (Navbar still showed "Login").
* **Severity & Evidence:** `Medium (P3)` — Post-registration user experience disruption and broken authentication flow.
* **Fix & Verification:** Replaced manual navigation with Redux Toolkit's `registerAsync` thunk inside `Register.jsx`. Dispatched `registerAsync(payload)`, verified fulfillment using `registerAsync.fulfilled.match(resultAction)`, and ensured proper token storage and Redux store synchronization.
* **Status:** `Fixed & Verified`

---

### 🔴 BUG-007 — Auth middleware calls `next()` on JWT verification failure
* **Location & Steps to Reproduce:** Backend `authMiddleware.js` $\rightarrow$ Send an API request to any protected route (like `/me`) using an invalid, expired, or outdated token.
* **Expected vs. Actual Result:** Expected backend to block request and return `401 Unauthorized`, but instead it executed `next()`, passing an unauthenticated request forward with an `undefined` `req.user` which caused downstream server crashes.
* **Severity & Evidence:** `Critical (P1)` — Server crash with `Cannot read properties of undefined (reading 'id')` error in console/response.
* **Fix & Verification:** Updated the `catch` block in `authMiddleware.js` to return `res.status(401).json({ message: "Not authorized, token failed or expired" })` instead of invoking `next()`.
* **Status:** `Fixed & Verified`

---

### 🟠 BUG-008 — Hardcoded dev bypass tokens cause 401 Unauthorized errors
* **Location & Steps to Reproduce:** Login page $\rightarrow$ Click on "Student Portal" or "Admin Portal" direct login (dev bypass) buttons.
* **Expected vs. Actual Result:** Expected immediate redirection, but encountered `401 Unauthorized` ("token failed or expired") error because static hardcoded tokens failed to match database states post-seeding.
* **Severity & Evidence:** `High (P2)` — Network Response: `{"message":"Not authorized, token failed or expired"}`.
* **Fix & Verification:** Replaced static hardcoded JWT tokens in `Login.jsx` with a dynamic API-driven handler (`handleDirectPortalLogin`). Triggers a real `loginUser` request, saves a fresh token, and redirects seamlessly.
* **Status:** `Fixed & Verified`

---

### 🟠 BUG-009 — Assignment submission filter queries non-existent `courseId`
* **Location & Steps to Reproduce:** Backend `admin.service.js` $\rightarrow$ `getAssignments` function $\rightarrow$ Admin dashboard querying assignment submissions by course.
* **Expected vs. Actual Result:** Expected submissions to be accurately filtered by specified course ID, but returned empty results because query filter targeted `courseId` while DB schema defines the field as `course`.
* **Severity & Evidence:** `High (P2)` — Query filter mismatch resulting in empty listings on admin dashboard.
* **Fix & Verification:** Updated `getAssignments` function in `admin.service.js` to correctly map `query.courseId` to `filter.course` to match Mongoose `AssignmentSubmission` model schema.
* **Status:** `Fixed & Verified`

---

### 🟠 BUG-010 — Registration payload omits `role` and `adminKey` fields
* **Location & Steps to Reproduce:** Frontend `Register.jsx` (`handleSubmit` function) $\rightarrow$ Enter name, email, password, and admin key $\rightarrow$ Click "Create Account".
* **Expected vs. Actual Result:** Expected to register user as an administrator when a valid key is provided, but account was always created with default "student" role because payload omitted `role` and `adminKey`.
* **Severity & Evidence:** `High (P2)` — Role assignment failure where valid admin key inputs were ignored.
* **Fix & Verification:** Updated `handleSubmit` in `Register.jsx` to dynamically construct and attach `role: "admin"` and `adminKey` to API payload when admin key input is populated, or `role: "student"` otherwise.
* **Status:** `Fixed & Verified`

---

### 🔴 BUG-011 — Inverted conditional check in `enrollCourse` blocks enrollments
* **Location & Steps to Reproduce:** Backend `enrollment.service.js` (`enrollCourse` function) $\rightarrow$ Attempt to enroll a student in a course for the first time.
* **Expected vs. Actual Result:** Expected to create a new enrollment record, but threw an "Already enrolled in this course" error because conditional check was inverted (`if (!enrollment)`).
* **Severity & Evidence:** `Critical (P1)` — Complete blockage of course enrollment functionality.
* **Fix & Verification:** Corrected conditional logic in `enrollCourse` to check for existing enrollment records (`if (existingEnrollment)`) and throw error only when already registered.
* **Status:** `Fixed & Verified`

---

### 🟠 BUG-012 — Missing `await` keyword on `enrollment.save()` in `completeLesson`
* **Location & Steps to Reproduce:** Backend `enrollment.service.js` (`completeLesson` function) $\rightarrow$ Student marks a lesson as completed.
* **Expected vs. Actual Result:** Expected progress percentages to be reliably saved before response, but `enrollment.save()` executed without `await`, causing race conditions and inconsistent progress tracking.
* **Severity & Evidence:** `High (P2)` — Data inconsistency where completed lesson counts failed to persist occasionally.
* **Fix & Verification:** Added missing `await` operator to `enrollment.save()` within `completeLesson` function.
* **Status:** `Fixed & Verified`

---

### 🟠 BUG-013 — Incorrect quiz percentage calculation using 10 instead of 100
* **Location & Steps to Reproduce:** Backend `quiz.service.js` $\rightarrow$ `submitQuiz` function $\rightarrow$ Student submits answers for a lesson quiz.
* **Expected vs. Actual Result:** Expected percentage score to be calculated by multiplying `(score / total)` by `100` (e.g., $8/10 \times 100 = 80\%$), but was multiplied by `10` (e.g., $8/10 \times 10 = 8\%$).
* **Severity & Evidence:** `High (P2)` — Data inaccuracy affecting student progress and quiz analytics.
* **Fix & Verification:** Updated percentage calculation formula in `submitQuiz` to `Math.round((score / total) * 100)`.
* **Status:** `Fixed & Verified`

---

### 🟡 BUG-014 — Unvalidated optional `adminKey` in Zod registration schema
* **Location & Steps to Reproduce:** Backend `authValidator.js` (`registerSchema`) & `authController.js` $\rightarrow$ User submits registration payload with/without `adminKey`.
* **Expected vs. Actual Result:** Expected Zod schema to strictly enforce constraints, but keeping it as `.optional()` caused validation inconsistencies and unhandled edge cases during admin registration.
* **Severity & Evidence:** `Medium (P3)` — Validation schema mismatch affecting data integrity during user sign-up.
* **Fix & Verification:** Removed `.optional()` from `adminKey` field in Zod `registerSchema` and updated controller logic to validate key securely against environment variables.
* **Status:** `Fixed & Verified`

---

### 🟠 BUG-015 — Restrictive CORS headers blocking `PUT`, `DELETE`, and `PATCH`
* **Location & Steps to Reproduce:** Backend `app.js` $\rightarrow$ Custom middleware setting `res.header("Access-Control-Allow-Methods", "GET, POST")` $\rightarrow$ Client attempts update or deletion operations.
* **Expected vs. Actual Result:** Expected all standard HTTP methods to be allowed, but custom middleware restricted methods strictly to `GET, POST`, causing CORS errors on `PUT`/`DELETE` requests.
* **Severity & Evidence:** `High (P2)` — Critical functionality failure preventing update/delete operations across the application.
* **Fix & Verification:** Removed restrictive custom middleware setting `Access-Control-Allow-Methods` and allowed standard `cors()` middleware to handle preflight headers automatically.
* **Status:** `Fixed & Verified`


# CourseMaster - Frontend
Live Link: https://course-master4981.netlify.app/

Video link: https://drive.google.com/file/d/1vjMrBxr0-7xqjxMX2EBEBw30D_av1pOu/view

A modern E-learning platform frontend built with React, Redux Toolkit, and Tailwind CSS.

Admin email: admin@coursemaster.com
Pass: admin123

## Project Name & Description

**CourseMaster** is a full-featured E-learning platform where students can browse, purchase, and consume courses. The frontend provides an intuitive interface for students to enroll in courses, watch video lectures, complete assignments, and take quizzes. Administrators can manage courses, track enrollments, review assignments, and view analytics through a dedicated admin dashboard.

This is the frontend application of the CourseMaster platform, built with modern React technologies and responsive design principles.

## Technology Stack

- **React 19** - UI library
- **Vite** - Build tool and dev server
- **Redux Toolkit** - State management
- **React Router DOM** - Routing
- **Tailwind CSS + DaisyUI** - Styling
- **Axios** - HTTP client

## Installation

1. Clone the repository
2. Navigate to the frontend directory:
   ```bash
   cd course-master-frontend
   ```

3. Install dependencies:
   ```bash
   npm install
   ```

4. Create a `.env` file in the root directory (see `.env.example`)

5. Start the development server:
   ```bash
   npm run dev
   ```

6. Build for production:
   ```bash
   npm run build
   ```

## Environment Variables

Create a `.env` file in the root directory with the following variables (see `.env.example` for reference):

```env
# Backend API Base URL
VITE_API_BASE_URL=http://localhost:5000/api
# For production, use your deployed backend URL:
# VITE_API_BASE_URL=http://localhost:5000/api/api
```

**Required Variables:**
- `VITE_API_BASE_URL` - The base URL of the backend API (required)

## Project Structure

```
src/
├── components/          # Reusable UI components
│   ├── CourseCard/     # Course listing components
│   └── shared/         # Shared components (Navbar, Footer, Logo)
├── layouts/            # Layout components
├── pages/              # Page components
│   ├── admin/          # Admin dashboard pages
│   ├── CoursePlayer/   # Course video player
│   ├── CousrseDetails/ # Course details page
│   ├── Home/           # Home page with course listing
│   ├── Login/          # Login page
│   ├── Register/       # Registration page
│   └── student/        # Student dashboard
├── routes/              # Route protection components
├── services/           # API service functions
├── store/              # Redux store and slices
└── utils/              # Utility functions
```

## Features

### Public Features
- Browse courses with pagination, search, sorting, and filtering
- View detailed course information
- User registration and login

### Student Features
- Enroll in courses
- Watch video lectures (YouTube integration)
- Mark lessons as complete
- Track learning progress
- Submit assignments (text or Google Drive links)
- Take quizzes with immediate score feedback
- View enrolled courses dashboard

### Admin Features
- Create, update, and delete courses
- Add course syllabus (modules, lessons, videos, assignments, quizzes)
- Manage course batches
- View all enrollments
- Review and grade student assignments
- Filter enrollments by course/student
- Filter assignments by status

## API Integration

The frontend communicates with the backend API. All API endpoints are documented in the backend README.

### Key API Endpoints Used:

- **Authentication**: `/api/auth/register`, `/api/auth/login`, `/api/auth/me`
- **Courses**: `/api/courses` (GET all, GET by ID)
- **Enrollments**: `/api/enrollments/:courseId` (POST), `/api/enrollments/my` (GET)
- **Assignments**: `/api/assignments/submit` (POST), `/api/assignments/my` (GET), `/api/assignments/stats` (GET)
- **Quizzes**: `/api/quizzes/submit` (POST), `/api/quizzes/my` (GET), `/api/quizzes/stats` (GET)
- **Admin**: `/api/admin/courses`, `/api/admin/enrollments`, `/api/admin/assignments`, `/api/admin/analytics`

For complete API documentation with request/response formats, see [courseMaster_Backend/README.md](../courseMaster_Backend/README.md).

## Development

- Development server runs on `http://localhost:5173` (default Vite port)
- Hot Module Replacement (HMR) is enabled
- ESLint is configured for code quality

## Deployment

The frontend can be deployed to:
- **Netlify**

