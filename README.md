# Hall Manager Pro - Marketing Landing Page

Professional marketing website for Hall Manager Pro - a comprehensive hall management system for venues, auditoriums, and event spaces.

## 🎯 Overview

Hall Manager Pro is an internal management tool designed for hall administrators to:
- Track bookings and events
- Manage expenses and finances
- Coordinate team activities
- Access management tools via mobile app

## 🚀 Features

- **Smart Booking Management** - Visual calendar for tracking all bookings
- **Expense Tracking** - Complete financial management
- **User Management** - Role-based team access
- **Custom Mobile App** - Android app for on-the-go management
- **Custom Branding** - Fully customizable with your logo and colors
- **Reports & Analytics** - Data-driven insights

## 💻 Tech Stack

- Pure HTML5
- CSS3 (Modern gradients, animations)
- Vanilla JavaScript
- FormSubmit.co (Form handling)

## 📦 Deployment

### Option 1: Netlify (Recommended)
1. Go to [netlify.com](https://netlify.com)
2. Drag & drop this folder
3. Instant deployment!

### Option 2: GitHub Pages
1. Push to GitHub
2. Settings → Pages → Enable
3. Access at `https://username.github.io/hall-booking-marketing`

### Option 3: Vercel
1. Go to [vercel.com](https://vercel.com)
2. Import repository
3. Deploy instantly

## 🧪 Local Development

Run a local server:

```bash
# Python 3
python -m http.server 8000

# Node.js
npx http-server

# PHP
php -S localhost:8000
```

Then open: `http://localhost:8000`

## 📧 Form Configuration

The contact form uses FormSubmit.co and sends to: `lekshmimollyp@gmail.com`

**First submission:** You'll receive a confirmation email. Click the link to activate.

## 🎨 Customization

### Update Pricing
Edit line ~547:
```html
<p>Starting at ₹15,000/month</p>
```

### Change Colors
Edit CSS variables (line ~16):
```css
:root {
    --primary: #667eea;
    --secondary: #764ba2;
}
```

### Update Contact Email
Edit line ~621:
```html
<form action="https://formsubmit.co/your-email@example.com" method="POST">
```

## 📊 Success Story

**MT Hall Pazhakutty** case study:
- 5hrs → 30min weekly admin time
- 0 double bookings
- 100% expense visibility
- 24/7 team access

## 📄 License

© 2026 Hall Manager Pro. All rights reserved.

## 🤝 Support

For inquiries about Hall Manager Pro, use the contact form on the landing page.
