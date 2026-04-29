# 💳 Payment Integration Guide - Braintree

## Overview
Your MERN Travel & Tourism app now includes **Braintree payment gateway** integration for secure credit card and PayPal payments.

## Features Implemented

✅ **Backend Payment APIs**
- `/api/payment/braintree-token` - Get client token for Drop-in UI
- `/api/payment/process-payment` - Process payments
- `/api/payment/history/:userId` - View payment history

✅ **Frontend Components**
- `Payment.jsx` - Braintree Drop-in payment form
- `PaymentsAdmin.jsx` - View and manage payment history

✅ **Database Integration**
- Bookings marked as "Paid" after successful payment
- Payment history tracked for each user

---

## Setup Instructions

### Step 1: Get Braintree Account

1. Sign up at [Braintree Payments](https://www.braintreepaymentsolutions.com/)
2. Create a **Sandbox account** (for testing)
3. Go to **Account** → **API** → **Keys**
4. Copy:
   - **Merchant ID**
   - **Public Key**
   - **Private Key**

### Step 2: Update Environment Variables

**Backend** (`backend/.env`):
```env
BRAINTREE_MERCHANT_ID=your_merchant_id_here
BRAINTREE_PUBLIC_KEY=your_public_key_here
BRAINTREE_PRIVATE_KEY=your_private_key_here
BRAINTREE_ENVIRONMENT=Sandbox
```

**Root** (`.env`):
```env
BRAINTREE_MERCHANT_ID=your_merchant_id_here
BRAINTREE_PUBLIC_KEY=your_public_key_here
BRAINTREE_PRIVATE_KEY=your_private_key_here
BRAINTREE_ENVIRONMENT=Sandbox
```

### Step 3: Restart Backend

```bash
cd backend
npm run dev
```

You should see:
```
✅ MongoDB Connected
🚀 Server listening on port 8000
```

---

## Test Payment Credentials

For **Sandbox Testing**, use these card numbers:

| Card Type | Number | Expiry | CVV |
|-----------|--------|--------|-----|
| Visa | 4111 1111 1111 1111 | 12/2025 | 123 |
| MasterCard | 5555 5555 5555 4444 | 12/2025 | 123 |
| Amex | 378282246310005 | 12/2025 | 1234 |

**PayPal (Sandbox)**: Use any email with password `111111`

---

## How to Use

### For Users (Booking Flow)

1. Browse packages on homepage
2. Click "Book Package"
3. Enter booking details (persons, dates, etc.)
4. Click "Proceed to Payment"
5. On Payment page:
   - Drop-in form loads automatically
   - Enter card details or PayPal
   - Click "Pay Now"
6. On success:
   - Booking marked as "Paid"
   - Confirmation email sent
   - Redirect to booking history

### For Admins (View Payments)

1. Go to Admin Dashboard
2. Click "Payments" in sidebar
3. View all paid bookings
4. See transaction summary:
   - Total transactions
   - Total amount collected
   - Payment status

---

## API Endpoints

### 1. Get Braintree Token
```
GET /api/payment/braintree-token
Headers: Authorization: Bearer {token}
Response: { success: true, clientToken: "..." }
```

### 2. Process Payment
```
POST /api/payment/process-payment
Headers: Authorization: Bearer {token}
Body: {
  amount: 10000,
  paymentMethodNonce: "...",
  bookingDetails: { _id, buyer, totalPrice, ... }
}
Response: { success: true, transactionId: "...", transaction: {...} }
```

### 3. Get Payment History
```
GET /api/payment/history/:userId
Headers: Authorization: Bearer {token}
Response: { success: true, payments: [...], count: 5 }
```

---

## Frontend Integration

### Using Payment Component

```jsx
import Payment from "@/pages/components/Payment";

<Payment 
  bookingDetails={bookingData}
  onPaymentSuccess={(data) => {
    console.log("Payment successful:", data);
    // Redirect or show success message
  }}
  onPaymentError={(error) => {
    console.error("Payment failed:", error);
    // Show error message
  }}
/>
```

---

## Troubleshooting

### ❌ "Braintree credentials are missing"
- Check that all 4 env vars are set in `backend/.env`
- Restart backend with `npm run dev`
- Verify `BRAINTREE_ENVIRONMENT=Sandbox` (case-sensitive)

### ❌ Payment form not loading
- Check browser console for errors
- Verify Braintree token endpoint is accessible
- Check auth token is valid

### ❌ "Address already in use"
- Kill existing Node process: `Get-Process -Name node | Stop-Process -Force`
- Restart with `npm run dev`

### ❌ Transaction declined
- Use test card numbers from the table above
- Ensure card details match exactly
- Check amount is in valid format

---

## Security Best Practices

✅ **Implemented**
- Nonce-based tokenization (card data never sent to your server)
- User authentication required for payments
- Server-side amount verification
- Environment variables for credentials

⚠️ **Recommended**
- Use HTTPS in production
- Implement 3D Secure for fraud prevention
- Store transaction IDs for reconciliation
- Set up webhook listeners for settlement events

---

## Next Steps

1. **Test Payments**: Use sandbox credentials with test cards
2. **Production Setup**: 
   - Create Braintree production account
   - Update environment to `Production`
   - Update credentials
   - Implement SSL/TLS

3. **Advanced Features**:
   - Subscription billing
   - Refunds API
   - Custom webhooks
   - Advanced fraud tools

---

## Support

For issues:
1. Check [Braintree Docs](https://developer.braintreegateway.com/)
2. Review error messages in backend logs
3. Check browser DevTools console

---

**Your app is now ready to accept payments!** 🎉

Test it by:
1. Starting backend: `npm run dev`
2. Starting frontend: `cd client && npm run dev`
3. Navigate to `localhost:5173`
4. Book a package and test payment flow
