
**FitConnect** is a comprehensive fitness management system that bridges the gap between clients and trainers. Clients can browse fitness plans, subscribe, track their progress via check-ins, and attend live or one-on-one sessions — while trainers manage plans, monitor client activity, and provide feedback.


- 🔐 **Role-based Authentication** — Separate roles for `client` and `trainer`
- 📋 **Fitness Plan Management** — Trainers create and manage plans with custom durations
- 📆 **Subscription System** — Clients subscribe to plans with start/end date tracking
- 💳 **Payment Processing** — Track payment status (`success`, `failed`, `pending`)
- 📊 **Progress Check-ins** — Clients log weight, body measurements, and notes
- 💬 **Trainer Feedback** — Trainers comment on client progress after each check-in
- 📹 **Session Scheduling** — Supports `live_group` and `one_on_one` session types
- 📈 **Availability Management** — Trainers can toggle their availability status


## Database Schema



| Column | Type | Constraints |
|---|---|---|
| `user_id` | SERIAL | PRIMARY KEY |
| `name` | VARCHAR(15) | NOT NULL |
| `email` | VARCHAR(322) | UNIQUE, NOT NULL |
| `password` | VARCHAR(15) | NOT NULL |
| `phone_number` | VARCHAR(15) | — |
| `role` | VARCHAR(10) | CHECK (`client`, `trainer`), NOT NULL |
| `created_at` | TIMESTAMP | — |
| `updated_at` | TIMESTAMP | — |

---

### 🧍 `client`
Extended profile for users with the `client` role.

| Column | Type | Constraints |
|---|---|---|
| `client_id` | SERIAL | PRIMARY KEY |
| `user_id` | INT | FOREIGN KEY → `users.user_id`, NOT NULL |
| `gender` | VARCHAR(20) | CHECK (`male`, `female`), NOT NULL |
| `goal` | VARCHAR(20) | NOT NULL |
| `height` | VARCHAR(20) | NOT NULL |
| `current_weight` | VARCHAR(10) | NOT NULL |
| `health_conditions` | TEXT | NOT NULL |
| `created_at` | TIMESTAMP | — |
| `updated_at` | TIMESTAMP | — |

---

### 🧑‍🏫 `trainer`
Extended profile for users with the `trainer` role.

| Column | Type | Constraints |
|---|---|---|
| `trainer_id` | SERIAL | PRIMARY KEY |
| `user_id` | INT | FOREIGN KEY → `users.user_id`, NOT NULL |
| `bio` | TEXT | NOT NULL |
| `experience` | INT | NOT NULL |
| `exp_area` | TEXT | NOT NULL |
| `profile_image_url` | TEXT | — |
| `is_available` | BOOLEAN | — |
| `created_at` | TIMESTAMP | — |
| `updated_at` | TIMESTAMP | — |

---

### 📋 `plans`
Fitness plans created by trainers.

| Column | Type | Constraints |
|---|---|---|
| `plan_id` | SERIAL | PRIMARY KEY |
| `trainer_id` | INT | FOREIGN KEY → `trainer.trainer_id`, NOT NULL |
| `plan_name` | VARCHAR(20) | NOT NULL |
| `description` | TEXT | NOT NULL |
| `duration` | INT | NOT NULL (in days) |
| `created_at` | TIMESTAMP | — |
| `updated_at` | TIMESTAMP | — |

---

### 📆 `subscriptions`
Links clients to plans they've enrolled in.

| Column | Type | Constraints |
|---|---|---|
| `subscriptions_id` | SERIAL | PRIMARY KEY |
| `client_id` | INT | FOREIGN KEY → `client.client_id`, NOT NULL |
| `plan_id` | INT | FOREIGN KEY → `plans.plan_id`, NOT NULL |
| `start_date` | DATE | NOT NULL |
| `end_date` | DATE | NOT NULL |
| `status` | VARCHAR(20) | CHECK (`active`, `completed`, `cancelled`) |
| `created_at` | TIMESTAMP | — |
| `updated_at` | TIMESTAMP | — |

---

### 💳 `payments`
Tracks all payment transactions tied to subscriptions.

| Column | Type | Constraints |
|---|---|---|
| `payment_id` | SERIAL | PRIMARY KEY |
| `subscriptions_id` | INT | FOREIGN KEY → `subscriptions.subscriptions_id`, NOT NULL |
| `amount` | DECIMAL | — |
| `payment_date` | DATE | — |
| `payment_status` | VARCHAR(20) | CHECK (`success`, `failed`, `pending`) |
| `timestamp` | TIMESTAMP | — |

---

### 📊 `check_ins`
Clients log their physical progress during a subscription period.

| Column | Type | Constraints |
|---|---|---|
| `check_in_id` | SERIAL | PRIMARY KEY |
| `client_id` | INT | FOREIGN KEY → `client.client_id`, NOT NULL |
| `subscription_id` | INT | FOREIGN KEY → `subscriptions.subscriptions_id`, NOT NULL |
| `weight` | DECIMAL | NOT NULL |
| `measurements` | TEXT | NOT NULL |
| `notes` | TEXT | — |
| `submitted_at` | TIMESTAMP | — |

---

### 💬 `trainer_feedback`
Trainers leave feedback comments on client check-ins.

| Column | Type | Constraints |
|---|---|---|
| `trainer_feedback_id` | SERIAL | PRIMARY KEY |
| `client_id` | INT | FOREIGN KEY → `client.client_id`, NOT NULL |
| `trainer_id` | INT | FOREIGN KEY → `trainer.trainer_id`, NOT NULL |
| `comment` | TEXT | NOT NULL |
| `timestamp` | TIMESTAMP | — |

---

### 🎥 `sessions`
Scheduled training sessions between clients and trainers.

| Column | Type | Constraints |
|---|---|---|
| `sessions_id` | SERIAL | PRIMARY KEY |
| `client_id` | INT | FOREIGN KEY → `client.client_id`, NOT NULL |
| `trainer_id` | INT | FOREIGN KEY → `trainer.trainer_id`, NOT NULL |
| `subscription_id` | INT | FOREIGN KEY → `subscriptions.subscriptions_id` |
| `session_type` | VARCHAR(30) | CHECK (`live_group`, `one_on_one`) |
| `status` | VARCHAR(20) | CHECK (`scheduled`, `completed`, `cancelled`) |
| `timestamp` | TIMESTAMP | — |

---

## Entity Relationships

```
users ──────────────┬──> client
                    └──> trainer

trainer ────────────────> plans

client ─────────────┬──> subscriptions <── plans
                    │
subscriptions ──────────> payments

client ─────────────┬──> check_ins <── subscriptions
check_ins ──────────┬──> trainer_feedback <── trainer

client ─────────────┬──> sessions <── trainer
                    └──────────────── subscriptions
```

**Relationship Summary:**
- A `user` has exactly one extended profile: either a `client` or a `trainer`
- A `trainer` can create many `plans`
- A `client` can subscribe to many `plans` via `subscriptions`
- Each `subscription` generates a `payment` record
- A `client` can log multiple `check_ins` per subscription
- `trainer_feedback` is linked to both the `client` and the `trainer` who reviewed the check-in
- `sessions` tie a `client`, `trainer`, and `subscription` together

---




> Built with 💪 for fitness enthusiasts and professional trainers alike.
