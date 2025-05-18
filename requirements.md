--  ENTITY-RELATIONSHIP DIAGRAM (ERD)
-- ERD Description:
-- Entities: User, Property, Booking, Payment, Review, Message
-- Relationships:
-- - User (1) ---< Property (host_id)
-- - User (1) ---< Booking (user_id)
-- - Property (1) ---< Booking (property_id)
-- - Booking (1) ---< Payment (booking_id)
-- - User (1) ---< Review (user_id), Property (1) ---< Review (property_id)
-- - User (1) ---< Message (sender_id), User (1) ---< Message (recipient_id)

--  DATABASE SCHEMA CREATION WITH NORMALIZATION & CONSTRAINTS

CREATE TABLE users (
    user_id UUID PRIMARY KEY,
    first_name VARCHAR(255) NOT NULL,
    last_name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    phone_number VARCHAR(20),
    role ENUM('guest', 'host', 'admin') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE properties (
    property_id UUID PRIMARY KEY,
    host_id UUID NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    location VARCHAR(255) NOT NULL,
    price_per_night DECIMAL(10,2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (host_id) REFERENCES users(user_id)
);

CREATE TABLE bookings (
    booking_id UUID PRIMARY KEY,
    property_id UUID NOT NULL,
    user_id UUID NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    total_price DECIMAL(10,2) NOT NULL,
    status ENUM('pending', 'confirmed', 'canceled') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (property_id) REFERENCES properties(property_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

CREATE TABLE payments (
    payment_id UUID PRIMARY KEY,
    booking_id UUID NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    payment_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    payment_method ENUM('credit_card', 'paypal', 'stripe') NOT NULL,
    FOREIGN KEY (booking_id) REFERENCES bookings(booking_id)
);

CREATE TABLE reviews (
    review_id UUID PRIMARY KEY,
    property_id UUID NOT NULL,
    user_id UUID NOT NULL,
    rating INTEGER CHECK (rating >= 1 AND rating <= 5) NOT NULL,
    comment TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (property_id) REFERENCES properties(property_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

CREATE TABLE messages (
    message_id UUID PRIMARY KEY,
    sender_id UUID NOT NULL,
    recipient_id UUID NOT NULL,
    message_body TEXT NOT NULL,
    sent_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (sender_id) REFERENCES users(user_id),
    FOREIGN KEY (recipient_id) REFERENCES users(user_id)
);

--  INDEXES
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_properties_property_id ON properties(property_id);
CREATE INDEX idx_bookings_property_id ON bookings(property_id);
CREATE INDEX idx_bookings_booking_id ON bookings(booking_id);
CREATE INDEX idx_payments_booking_id ON payments(booking_id);

--  SAMPLE DATA POPULATION

-- Users
INSERT INTO users VALUES ('uuid-guest-1', 'John', 'Doe', 'john@example.com', 'hashedpass1', '1234567890', 'guest', DEFAULT);
INSERT INTO users VALUES ('uuid-host-1', 'Alice', 'Smith', 'alice@example.com', 'hashedpass2', '0987654321', 'host', DEFAULT);

-- Properties
INSERT INTO properties VALUES ('uuid-property-1', 'uuid-host-1', 'Cozy Apartment', 'A cozy downtown apartment.', 'Downtown', 75.00, DEFAULT, DEFAULT);

-- Bookings
INSERT INTO bookings VALUES ('uuid-booking-1', 'uuid-property-1', 'uuid-guest-1', '2025-06-01', '2025-06-05', 300.00, 'confirmed', DEFAULT);

-- Payments
INSERT INTO payments VALUES ('uuid-payment-1', 'uuid-booking-1', 300.00, DEFAULT, 'credit_card');

-- Reviews
INSERT INTO reviews VALUES ('uuid-review-1', 'uuid-property-1', 'uuid-guest-1', 5, 'Fantastic stay!', DEFAULT);

-- Messages
INSERT INTO messages VALUES ('uuid-message-1', 'uuid-guest-1', 'uuid-host-1', 'Hi Alice, is the apartment available in July?', DEFAULT);
