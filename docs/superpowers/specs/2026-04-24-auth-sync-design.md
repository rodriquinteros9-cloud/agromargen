# Design: User Authentication and Scenario Cloud Synchronization

**Date:** 2026-04-24
**Status:** Draft
**Topic:** Implementing user login/registration and cloud backup for agricultural scenarios using Supabase.

## 1. Purpose
The goal is to transition the AGROMARGEN app from a purely local-storage-based tool to a multi-user platform where producers can secure their data, synchronize scenarios across devices, and have a personalized profile.

## 2. User Experience (UX)
### 2.1 Access Control
- **Authentication Gate**: The application will now start with a mandatory authentication screen (`#authScreen`).
- **Session Persistence**: If a valid session exists in the browser, the user will bypass the auth screen and go directly to the Home screen.
- **Logout**: A logout button will be added to the top bar to clear the session and return to the auth screen.

### 2.2 Authentication Interface
- **UI Components**:
    - **Login/Register Toggle**: Ability to switch between "Sign In" and "Sign Up".
    - **Credentials**: Fields for Email and Password.
    - **OAuth**: "Continue with Google" button for one-click access.
- **Styling**: The auth screen will use the existing CSS variables (`--brand`, `--surface`, `--ink`) to ensure visual consistency with the rest of the app.

## 3. Technical Architecture
### 3.1 Backend (Supabase)
We will use Supabase as the Backend-as-a-Service (BaaS) to handle authentication and data storage.
- **Integration**: The Supabase JavaScript client will be loaded via CDN in `AGROMARGEN_2026-27.html`.
- **Auth Methods**: 
    - Email/Password.
    - Google OAuth.

### 3.2 Database Schema
A table named `scenarios` will be created in the PostgreSQL database.

| Column | Type | Description |
|---|---|---|
| `id` | UUID (PK) | Unique identifier for the scenario. |
| `user_id` | UUID (FK) | Reference to `auth.users`. Ensures data isolation. |
| `scenario_name` | Text | User-defined name for the scenario. |
| `data` | JSONB | The complete snapshot of the scenario state (yields, prices, costs). |
| `created_at` | Timestamp | Record creation date. |
| `updated_at` | Timestamp | Last modification date. |

### 3.3 Data Flow
- **Save Scenario**:
    1. Collect all current inputs from the main table and settings.
    2. Package data into a JSON object.
    3. Perform an `upsert` operation in the `scenarios` table using the `user_id`.
- **Load Scenario**:
    1. Fetch all rows from `scenarios` where `user_id` equals the current session user.
    2. Present a list to the user for selection.
    3. Inject the selected JSON data back into the application state.

## 4. Error Handling and Security
- **Feedback**: All auth and database operations will trigger the existing `toast` notification system (Success/Error).
- **Security**:
    - **RLS (Row Level Security)**: Supabase RLS policies will be enabled on the `scenarios` table to ensure users can only `SELECT`, `INSERT`, `UPDATE`, and `DELETE` their own rows (`auth.uid() = user_id`).
    - **Tokens**: Session tokens will be handled by the Supabase client in `localStorage` securely.

## 5. Success Criteria
- Users cannot access the main app without logging in.
- Users can register and log in via Email or Google.
- Scenarios are saved to the cloud and persist after clearing browser cache.
- Logged-out users cannot access other users' scenarios.
