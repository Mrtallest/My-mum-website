# Affiliate Pro Hub — Multi-Page + Firebase + Payments

A modern, responsive affiliate marketing platform built with HTML, CSS, JavaScript, Firebase, and payment integrations (Stripe & Paystack).

## Overview

- **Multi-page static site** with single-page app navigation
- **Firebase Auth + Firestore** for users, products, blog posts, and portfolios
- **Stripe Checkout Sessions** integration via Node/Express server (secure)
- **Paystack Inline** payment processing example
- **Admin Dashboard** for managing products, blogs, and portfolio items
- **Dark/Light theme toggle** with localStorage persistence
- **Fully responsive** design optimized for mobile, tablet, and desktop

## Features

### User Features
- Browse products with detailed information
- Read blog posts
- View portfolio items
- User authentication (Firebase Auth)
- Dark/light theme toggle
- Smooth animations and transitions
- Mobile-responsive layout

### Admin Features
- Add/manage products with image, price, category, affiliate link
- Publish/manage blog posts
- Add/manage portfolio items
- Delete any item from the dashboard
- Secure admin authentication

## File Structure

```
.
├── index.html           # Main single-page app (all sections)
├── README.md            # This file
└── /public              # (Optional) For deployment
    ├── index.html
    ├── shop.html        # (Alternative) Separate shop page
    ├── blog.html        # (Alternative) Separate blog page
    ├── owner.html       # (Alternative) Admin page
    └── /assets
        ├── style.css
        └── app.js
```

## Setup Instructions

### 1. Firebase Configuration

Replace the placeholder values in `index.html` with your Firebase config:

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

Get these values from:
1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Create a new project
3. Add a web app
4. Copy the config object

### 2. Firebase Firestore Setup

Create these collections in Firestore:

**`products`** collection:
```json
{
  "title": "Course Title",
  "price": 5000,
  "category": "Course",
  "image": "https://...",
  "link": "https://affiliate-link.com",
  "description": "Product description",
  "createdAt": "2025-11-27T..."
}
```

**`blogs`** collection:
```json
{
  "title": "Blog Post Title",
  "image": "https://...",
  "content": "Long blog content here...",
  "createdAt": "2025-11-27T..."
}
```

**`portfolios`** collection:
```json
{
  "title": "Project Title",
  "image": "https://...",
  "description": "Project description",
  "link": "https://project-url.com",
  "createdAt": "2025-11-27T..."
}
```

### 3. Firebase Auth Setup

Enable Email/Password authentication:
1. In Firebase Console, go to **Authentication**
2. Click **Sign-in method**
3. Enable **Email/Password**

### 4. Admin User

Create an admin user with the email `admin@affiliateprohub.com` (or change `ADMIN_EMAIL` variable in the script).

Change it in `index.html`:
```javascript
const ADMIN_EMAIL = 'your-admin@email.com';
```

## Payment Integration (Optional)

### Stripe Checkout

Create a Node/Express server to handle Stripe Checkout Sessions securely.

**Example endpoint** (`/server/server.js`):
```javascript
const express = require('express');
const stripe = require('stripe')(process.env.STRIPE_SECRET);

app.post('/create-checkout-session', async (req, res) => {
  const { productId } = req.body;
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: [{
      price_data: {
        currency: 'usd',
        product_data: { name: 'Product Name' },
        unit_amount: 5000
      },
      quantity: 1
    }],
    mode: 'payment',
    success_url: `${process.env.ORIGIN}/success.html`,
    cancel_url: `${process.env.ORIGIN}/cancel.html`
  });
  res.json({ url: session.url });
});
```

**Environment variables:**
```
STRIPE_SECRET=sk_test_...
STRIPE_PUBLIC=pk_test_...
ORIGIN=http://localhost:3000
PORT=4242
```

### Paystack Inline

Paystack is integrated via CDN (`https://js.paystack.co/v2/inline.js`). No additional setup required for the frontend.

For **server-side verification**, create:
```javascript
app.post('/paystack-verify', async (req, res) => {
  const { reference } = req.body;
  const url = `https://api.paystack.co/transaction/verify/${reference}`;
  const r = await fetch(url, {
    headers: { Authorization: `Bearer ${process.env.PAYSTACK_SECRET}` }
  });
  const data = await r.json();
  res.json(data);
});
```

## Usage

### Admin Login
1. Click **Login** in the header
2. Sign up or log in with your credentials
3. If you're the admin, the **Admin** button will appear
4. Click **Admin** to manage products, blogs, and portfolio

### Adding Products
1. Go to Admin Dashboard
2. Fill in product details (title, price, category, image URL, affiliate link, description)
3. Click **Add Product**
4. Product appears on the Products page immediately

### Publishing Blog Posts
1. Go to Admin Dashboard
2. Enter blog title, image, and content
3. Click **Publish Blog**
4. Post appears on Blog page and in dropdown menu

### Adding Portfolio Items
1. Go to Admin Dashboard
2. Enter project title, image, description, and project link (optional)
3. Click **Add to Portfolio**
4. Item appears on Portfolio page

## Theme Toggle

Click the 🌙 button in the header to switch between light and dark themes. Your preference is saved to localStorage.

## Security Notes

⚠️ **IMPORTANT:**
- Never store Stripe secret keys in frontend code
- Always use a backend server for Stripe operations
- Paystack transactions should be verified server-side
- Never commit Firebase credentials to version control
- Use environment variables for sensitive data
- Restrict Firestore read/write rules based on authentication

## Firestore Rules (Example)

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /products/{document=**} {
      allow read: if request.auth != null;
      allow write: if request.auth.token.email == 'admin@affiliateprohub.com';
    }
    match /blogs/{document=**} {
      allow read: if request.auth != null;
      allow write: if request.auth.token.email == 'admin@affiliateprohub.com';
    }
    match /portfolios/{document=**} {
      allow read: if request.auth != null;
      allow write: if request.auth.token.email == 'admin@affiliateprohub.com';
    }
  }
}
```

## Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)
- Mobile browsers (iOS Safari, Chrome Mobile)

## Customization

### Colors
Edit CSS variables at the top of `<style>`:
```css
:root {
  --primary: #66fcf1;      /* Cyan */
  --secondary: #45a29e;    /* Teal */
  --dark: #0b0c10;         /* Dark background */
  --darker: #1f2833;       /* Darker shade */
  --text: #c5c6c7;         /* Light text */
}
```

### Admin Email
Change in the JavaScript section:
```javascript
const ADMIN_EMAIL = 'your-admin@email.com';
```

### Site Title
Change the `<title>` tag and logo text in the HTML.

## Deployment

### Firebase Hosting
```bash
npm install -g firebase-tools
firebase init hosting
firebase deploy
```

### Vercel
```bash
vercel
```

### GitHub Pages
Push to `gh-pages` branch or enable GitHub Pages in repository settings.

## Troubleshooting

**"Firebase config not working"**
- Check you've replaced all placeholder values
- Ensure Firestore database exists
- Verify authentication is enabled

**"Products not loading"**
- Check browser console for errors
- Verify Firestore rules allow reads
- Ensure products collection has data

**"Admin dashboard not showing"**
- Verify you're logged in with the admin email
- Check ADMIN_EMAIL constant matches your email
- Clear browser localStorage and refresh

**"Stripe/Paystack not working"**
- Ensure backend server is running
- Check environment variables are set
- Verify payment gateway credentials

## License

MIT License - Feel free to use and modify!

## Support

For issues or questions:
1. Check Firebase documentation: https://firebase.google.com/docs
2. Stripe docs: https://stripe.com/docs
3. Paystack docs: https://paystack.com/developers

---

**Built with ❤️ for affiliate marketers**
