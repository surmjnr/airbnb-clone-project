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

