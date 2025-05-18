# Airbnb-Like Database Design (Entity Overview and Relationships)

## 📦 Entities and Attributes

### 1. **User**
- `user_id`: UUID, Primary Key, Indexed
- `first_name`: VARCHAR, NOT NULL
- `last_name`: VARCHAR, NOT NULL
- `email`: VARCHAR, UNIQUE, NOT NULL
- `password_hash`: VARCHAR, NOT NULL
- `phone_number`: VARCHAR, NULL
- `role`: ENUM ('guest', 'host', 'admin'), NOT NULL
- `created_at`: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP

---

### 2. **Property**
- `property_id`: UUID, Primary Key, Indexed
- `host_id`: UUID, Foreign Key → `User(user_id)`
- `name`: VARCHAR, NOT NULL
- `description`: TEXT, NOT NULL
- `location`: VARCHAR, NOT NULL
- `pricepernight`: DECIMAL, NOT NULL
- `created_at`: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP
- `updated_at`: TIMESTAMP, ON UPDATE CURRENT_TIMESTAMP

---

### 3. **Booking**
- `booking_id`: UUID, Primary Key, Indexed
- `property_id`: UUID, Foreign Key → `Property(property_id)`
- `user_id`: UUID, Foreign Key → `User(user_id)`
- `start_date`: DATE, NOT NULL
- `end_date`: DATE, NOT NULL
- `total_price`: DECIMAL, NOT NULL
- `status`: ENUM ('pending', 'confirmed', 'canceled'), NOT NULL
- `created_at`: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP

---

### 4. **Payment**
- `payment_id`: UUID, Primary Key, Indexed
- `booking_id`: UUID, Foreign Key → `Booking(booking_id)`
- `amount`: DECIMAL, NOT NULL
- `payment_date`: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP
- `payment_method`: ENUM ('credit_card', 'paypal', 'stripe'), NOT NULL

---

### 5. **Review**
- `review_id`: UUID, Primary Key, Indexed
- `property_id`: UUID, Foreign Key → `Property(property_id)`
- `user_id`: UUID, Foreign Key → `User(user_id)`
- `rating`: INTEGER (1–5), NOT NULL, CHECK: rating ≥ 1 AND ≤ 5
- `comment`: TEXT, NOT NULL
- `created_at`: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP

---

### 6. **Message**
- `message_id`: UUID, Primary Key, Indexed
- `sender_id`: UUID, Foreign Key → `User(user_id)`
- `recipient_id`: UUID, Foreign Key → `User(user_id)`
- `message_body`: TEXT, NOT NULL
- `sent_at`: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP

---

## 🔗 Relationships Between Entities

- **User ↔ Property**
  - One-to-Many (A user with role 'host' can own many properties)
  - `Property.host_id` → `User.user_id`

- **User ↔ Booking**
  - One-to-Many (A user can make many bookings)
  - `Booking.user_id` → `User.user_id`

- **Property ↔ Booking**
  - One-to-Many (A property can be booked many times)
  - `Booking.property_id` → `Property.property_id`

- **Booking ↔ Payment**
  - One-to-One (Each booking has exactly one payment record)
  - `Payment.booking_id` → `Booking.booking_id`

- **User ↔ Review**
  - One-to-Many (A user can review multiple properties)
  - `Review.user_id` → `User.user_id`

- **Property ↔ Review**
  - One-to-Many (A property can have multiple reviews)
  - `Review.property_id` → `Property.property_id`

- **User ↔ Message (as Sender and Recipient)**
  - Many-to-Many (Users can send messages to one another)
  - `Message.sender_id` → `User.user_id`
  - `Message.recipient_id` → `User.user_id`

---

> **Note:** All relationships are enforced via foreign key constraints to ensure referential integrity.

---

### 🔍 Step 1: First Normal Form (1NF)
**Requirement:** Eliminate repeating groups and ensure atomicity (each column should contain indivisible values).

**Review:**
- All attributes are atomic (e.g., no multiple phone numbers in one field).
- No repeating groups exist in any table.

✅ **Status:** All tables comply with 1NF.

---

### 🔍 Step 2: Second Normal Form (2NF)
**Requirement:** Achieve 1NF and ensure that all non-key attributes are fully functionally dependent on the entire primary key.

**Review:**
- All tables have single-attribute primary keys (UUIDs).
- No partial dependencies exist.

✅ **Status:** All tables comply with 2NF.

---

### 🔍 Step 3: Third Normal Form (3NF)
**Requirement:** Achieve 2NF and remove transitive dependencies (non-key attributes should not depend on other non-key attributes).

**Review & Adjustments:**

#### ✅ `User` Table
- All attributes depend directly on `user_id`.
- No transitive dependencies.

✅ **3NF Compliant**

---

#### ✅ `Property` Table
- `host_id` references `User(user_id)`; all other attributes depend on `property_id`.
- `pricepernight`, `description`, etc., are not derived from each other.

✅ **3NF Compliant**

---

#### ✅ `Booking` Table
- Attributes like `start_date`, `end_date`, and `total_price` depend on `booking_id`.
- No calculated fields or transitive dependencies.

✅ **3NF Compliant**

---

#### ✅ `Payment` Table
- `amount`, `payment_date`, and `payment_method` are directly related to `payment_id`.
- If `amount` is derived from `Booking.total_price`, we keep it here for historical accuracy (invoices may change over time).

✅ **3NF Compliant**

---

#### ✅ `Review` Table
- `rating` and `comment` are fully dependent on the `review_id`.
- No attributes are transitively dependent.

✅ **3NF Compliant**

---

#### ✅ `Message` Table
- `message_body` and `sent_at` relate only to the message and its sender/recipient.

✅ **3NF Compliant**

---

### 🛠 Additional Notes
- **No derived or computed fields** are stored in the schema (e.g., `total_price` in `Booking` is assumed to be computed at the time of booking and stored for record consistency).
- **ENUMs** for roles, payment methods, and booking statuses help enforce data consistency without violating normalization principles.

---

### ✅ Final Verdict
The schema is **fully normalized up to 3NF**. All attributes in each table:
- Depend on the whole key
- Depend only on the key
- Are non-redundant and atomic