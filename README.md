
## Overview

This database supports a fitness platform connecting **clients** and **trainers**. It handles user management, training plans, subscriptions, payments, session scheduling, progress check-ins, and trainer feedback.

---

## Tables

### `users`
The base table for all platform users.

| Column | Type | Constraints |
|---|---|---|
| user_id | SERIAL | Primary Key |
| name | VARCHAR(15) | NOT NULL |
| email | VARCHAR(322) | UNIQUE, NOT NULL |
| password | VARCHAR(15) | NOT NULL |
| phone_number | VARCHAR(15) | — |
| role | VARCHAR(10) | NOT NULL, CHECK: `client` or `trainer` |
| created_at | — | — |
| updated_at | — | — |

---

### `clients`
Extended profile for users with the `client` role.

| Column | Type | Constraints |
|---|---|---|
| client_id | SERIAL | Primary Key |
| user_id | INT | FK → `users.user_id`, NOT NULL |
| gender | VARCHAR(20) | NOT NULL, CHECK: `male` or `female` |
| goal | VARCHAR(20) | NOT NULL |
| height | VARCHAR(20) | NOT NULL |
| current_weight | VARCHAR(10) | NOT NULL |
| health_conditions | TEXT | NOT NULL |
| created_at | — | — |
| updated_at | — | — |

---

### `trainers`
Extended profile for users with the `trainer` role.

| Column | Type | Constraints |
|---|---|---|
| trainer_id | SERIAL | Primary Key |
| user_id | INT | FK → `users.user_id`, NOT NULL |
| bio | TEXT | NOT NULL |
| experience | INT | NOT NULL |
| exp_area | TEXT | NOT NULL |
| profile_image_url | — | — |
| is_available | BOOLEAN | — |
| created_at | — | — |
| updated_at | — | — |

---

### `plans`
Training plans created by trainers.

| Column | Type | Constraints |
|---|---|---|
| plan_id | SERIAL | Primary Key |
| trainer_id | INT | FK → `trainers.trainer_id`, NOT NULL |
| plan_name | VARCHAR(20) | NOT NULL |
| description | TEXT | NOT NULL |
| duration | INT | NOT NULL |
| price | DECIMAL | NOT NULL |
| created_at | — | — |
| updated_at | — | — |

---

### `subscriptions`
Links clients to plans they have subscribed to.

| Column | Type | Constraints |
|---|---|---|
| subscriptions_id | SERIAL | Primary Key |
| client_id | INT | FK → `clients.client_id`, NOT NULL |
| plan_id | INT | FK → `plans.plan_id`, NOT NULL |
| start_date | DATE | NOT NULL |
| end_date | DATE | NOT NULL |
| status | VARCHAR(20) | CHECK: `active`, `completed`, or `cancelled` |
| created_at | — | — |
| updated_at | — | — |

---

### `payments`
Payment records tied to a subscription.

| Column | Type | Constraints |
|---|---|---|
| payment_id | SERIAL | Primary Key |
| subscriptions_id | INT | FK → `subscriptions.subscriptions_id`, NOT NULL |
| amount | DECIMAL | — |
| payment_date | DATE | — |
| payment_status | VARCHAR(20) | CHECK: `success`, `failed`, or `pending` |
| timestamp | — | — |

---

### `check_ins`
Progress check-ins submitted by clients during a subscription.

| Column | Type | Constraints |
|---|---|---|
| check_ins | SERIAL | Primary Key |
| client_id | INT | FK → `clients.client_id`, NOT NULL |
| subscription_id | INT | FK → `subscriptions.subscriptions_id`, NOT NULL |
| weight | DECIMAL | NOT NULL |
| measurements | TEXT | NOT NULL |
| notes | TEXT | — |
| submitted_at | TIMESTAMP | — |

---

### `trainer_feedback`
Feedback left by trainers in response to client check-ins.

| Column | Type | Constraints |
|---|---|---|
| trainer_feedback_id | SERIAL | Primary Key |
| client_id | INT | FK → `clients.client_id`, NOT NULL |
| trainer_id | INT | FK → `trainers.trainer_id`, NOT NULL |
| comment | TEXT | NOT NULL |
| created_at | — | — |
| updated_at | — | — |

> **Note:** `trainer_feedback` is linked to `check_ins.check_ins` (checkin_id).

---

### `sessions`
Scheduled training sessions between a client and a trainer.

| Column | Type | Constraints |
|---|---|---|
| sessions_id | SERIAL | Primary Key |
| client_id | INT | FK → `clients.client_id`, NOT NULL |
| trainer_id | INT | FK → `trainers.trainer_id`, NOT NULL |
| subscription_id | — | FK → `subscriptions.subscriptions_id` |
| session_type | VARCHAR(30) | CHECK: `live_group` or `one_on_one` |
| status | VARCHAR(20) | CHECK: `scheduled`, `completed`, or `cancelled` |
| scheduled_at | DATE | NOT NULL |
| meeting_link | TEXT | — |
| created_at | — | — |
| updated_at | — | — |

---

## Relationships

| From | To | Description |
|---|---|---|
| `users.user_id` | `clients.user_id` | A user can have a client profile |
| `users.user_id` | `trainers.user_id` | A user can have a trainer profile |
| `trainers.trainer_id` | `plans.trainer_id` | A trainer creates plans |
| `clients.client_id` | `subscriptions.client_id` | A client subscribes to plans |
| `plans.plan_id` | `subscriptions.plan_id` | A plan can have many subscriptions |
| `subscriptions.subscriptions_id` | `payments.subscriptions_id` | A subscription has payment records |
| `clients.client_id` | `check_ins.client_id` | A client submits check-ins |
| `subscriptions.subscriptions_id` | `check_ins.subscription_id` | Check-ins are tied to a subscription |
| `check_ins.check_ins` | `trainer_feedback.checkin_id` | Feedback is given on a check-in |
| `trainers.trainer_id` | `trainer_feedback.trainer_id` | A trainer provides the feedback |
| `clients.client_id` | `sessions.client_id` | A client participates in sessions |
| `trainers.trainer_id` | `sessions.trainer_id` | A trainer leads sessions |
| `subscriptions.subscriptions_id` | `sessions.subscription_id` | Sessions are linked to a subscription |
