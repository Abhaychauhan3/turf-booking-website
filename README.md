# GreenField Turf Booking — Full-stack Connected Demo

This is a working turf booking website where the frontend and backend are connected.

It includes:

- Customer website for selecting sport, date, and time slot
- Booking form with Indian mobile number validation
- Test payment confirmation button
- Backend API for availability and bookings
- SQLite database storage in one local file
- Owner panel for checking bookings and blocking/unblocking slots
- Double-booking protection using database constraints

## Requirements

Install Node.js 22 or newer.

Check your version:

```bash
node -v
```

## How to run

Open terminal inside this folder and run:

```bash
npm start
```

Then open this in your browser:

```text
http://localhost:4000
```

That is it. This project has no npm package dependencies, so you do not need `npm install` for the demo.

## How the flow works

1. Customer opens the website.
2. Frontend calls the backend: `GET /api/availability`.
3. Backend checks the SQLite database and returns open/booked/blocked slots.
4. Customer chooses a slot and enters name + phone.
5. Customer clicks test payment confirmation.
6. Frontend calls backend: `POST /api/bookings`.
7. Backend stores the booking in `turf.db`.
8. The chosen slot becomes booked immediately.
9. If another customer tries the same slot, backend rejects it with `409 Conflict`.

## Owner panel

Click **Owner panel** in the top-right.

You can:

- See booked/open/blocked slot count
- View booking list for selected date
- Tap a slot to block/unblock it

For production security, set an owner PIN before running:

### Windows PowerShell

```powershell
$env:OWNER_PIN="1234"
npm start
```

### macOS/Linux

```bash
OWNER_PIN=1234 npm start
```

Then enter that PIN in the website's owner panel PIN box.

## Customization

Edit this file:

```text
src/config.js
```

You can change:

- Turf name
- City/address
- Sports
- Day/night pricing
- Time slots

## API reference

### Health check

```http
GET /api/health
```

### Website config

```http
GET /api/config
```

### Availability

```http
GET /api/availability?date=2026-06-19&sport=football
```

### Create booking

```http
POST /api/bookings
Content-Type: application/json

{
  "date": "2026-06-19",
  "slot": "19:00",
  "sport": "football",
  "customerName": "Abhay",
  "phone": "9876543210"
}
```

### Owner booking list

```http
GET /api/bookings?date=2026-06-19
x-owner-pin: 1234
```

### Block/unblock slot

```http
POST /api/admin/block-slot
Content-Type: application/json
x-owner-pin: 1234

{
  "date": "2026-06-19",
  "slot": "21:00",
  "sport": "football",
  "block": true,
  "reason": "Maintenance"
}
```

Use `"block": false` to unblock.

## Test it

Run:

```bash
npm test
```

The test verifies:

- Website loads
- API runs
- Booking gets stored
- Night pricing works
- Same slot cannot be double-booked
- Multiple simultaneous booking attempts allow only one success
- Owner can block a slot
- Blocked slot cannot be booked

## What is still demo-only

The backend is real and stores bookings, but payment is still test/demo mode.

Before using with real customers, add:

1. Razorpay order creation endpoint
2. Razorpay Checkout on frontend
3. Payment signature verification on backend
4. Owner authentication beyond a simple PIN
5. Deployment with HTTPS
6. SMS/WhatsApp confirmation if required

## Suggested next production step

Add Razorpay in this sequence:

1. `POST /api/payments/create-order` creates a Razorpay order for the selected slot amount.
2. Frontend opens Razorpay Checkout.
3. Razorpay returns payment ID, order ID, and signature.
4. Frontend calls `POST /api/payments/verify`.
5. Backend verifies signature using Razorpay secret.
6. Only after verification, backend creates the booking.

This prevents fake payment confirmations.
