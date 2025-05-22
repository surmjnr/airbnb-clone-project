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