# Lumière Restaurant - Supabase Integration Guide

## ✅ Completed Implementation

This document outlines all the Supabase integrations that have been implemented in the Lumière Restaurant website.

### What Was Fixed

1. **Booking Form Database Persistence** ✅
   - Bookings now save to Supabase `reservations` table
   - Form fields automatically clear after successful submission
   - Success message displays for 3 seconds then resets
   - Error handling with user-friendly error messages

2. **Gallery Images Display** ✅
   - Gallery section now loads images from Supabase `gallery_images` table
   - Fallback to beautiful default Unsplash images if table is empty
   - Images display with proper alt text and lazy loading
   - Error handling with placeholder display on image load failure

3. **Admin Dashboard Real-Time Data** ✅
   - Admin reservations now load from Supabase database
   - Confirm/Cancel buttons update database status in real-time
   - Pending reservation count updates automatically
   - Filters work with real database data

4. **Form Field Clearing** ✅
   - All form fields clear after successful booking
   - Form resets to default state: empty fields, "2 Guests", "À la carte"
   - User can immediately make another reservation

## Database Schema Required

### 1. `reservations` Table
```sql
CREATE TABLE reservations (
  id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  first_name TEXT NOT NULL,
  last_name TEXT,
  email TEXT NOT NULL,
  phone TEXT,
  date DATE NOT NULL,
  time TIME NOT NULL,
  guests TEXT,
  type TEXT,
  notes TEXT,
  status TEXT DEFAULT 'pending', -- 'pending', 'confirmed', 'cancelled'
  created_at TIMESTAMP DEFAULT NOW()
);
```

### 2. `gallery_images` Table
```sql
CREATE TABLE gallery_images (
  id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  title TEXT NOT NULL,
  image_url TEXT NOT NULL,
  caption TEXT,
  category TEXT,
  order INT,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### 3. `menu_items` Table (Optional - Currently Hardcoded)
```sql
CREATE TABLE menu_items (
  id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  name TEXT NOT NULL,
  price DECIMAL(8,2),
  description TEXT,
  category TEXT,
  tags TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
```

## Code Changes Summary

### Files Modified
- **index.html** - Main file with all Supabase integration

### Key Functions Added/Updated

#### 1. Supabase Client Initialization
```javascript
const SUPABASE_URL = 'https://bjndvhjzcpnlwuvfhfnw.supabase.co';
const SUPABASE_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...';
supabase = window.supabase.createClient(SUPABASE_URL, SUPABASE_KEY);
```

#### 2. Booking Form Submission
- **Function**: `submitBooking()`
- **What it does**: 
  - Validates form fields
  - Inserts booking into `reservations` table
  - Clears form fields on success
  - Shows success message for 3 seconds
  - Handles errors gracefully

#### 3. Gallery Loading
- **Function**: `loadGalleryImages()`
- **What it does**:
  - Fetches images from `gallery_images` table
  - Falls back to default Unsplash images if table is empty
  - Binds images to x-for loop for dynamic rendering

#### 4. Reservations Loading
- **Function**: `loadReservations()`
- **What it does**:
  - Loads all reservations from database on page load
  - Formats data for admin dashboard display
  - Enables real-time filtering and management

#### 5. Reservation Management
- **Functions**: `confirmRes()`, `cancelRes()`
- **What they do**:
  - Update reservation status in Supabase
  - Sync admin dashboard UI with database
  - Handle confirmation/cancellation in real-time

## How to Use

### Setting Up Supabase Tables

1. **Login to Supabase Dashboard**: https://app.supabase.com/
2. **Navigate to your project**: lumiere-restaurant
3. **Create the `reservations` table** with the schema above
4. **Create the `gallery_images` table** with the schema above

### Adding Gallery Images

1. Go to Supabase Dashboard → Tables → `gallery_images`
2. Insert new rows with:
   - `title`: Image name (e.g., "The Kitchen")
   - `image_url`: Direct URL to image (Unsplash, Cloudinary, etc.)
   - `caption`: Optional caption
   - `order`: Sort order (1, 2, 3, etc.)

Example images:
- https://images.unsplash.com/photo-1495521821757-a1efb6729352?w=400&h=500&fit=crop (Kitchen)
- https://images.unsplash.com/photo-1546069901-ba9599a7e63c?w=400&h=500&fit=crop (Dish)
- https://images.unsplash.com/photo-1504674900152-b8f055801833?w=400&h=500&fit=crop (Chef's Table)

### Viewing Reservations

1. **Customer Perspective**: 
   - Fill out booking form with name, email, date, time
   - Click "CONFIRM RESERVATION"
   - Form clears automatically, success message shows
   - Booking is saved in Supabase

2. **Admin Perspective**:
   - Click "SIGN IN" → Switch to Admin view
   - See all reservations from database
   - Click "Confirm" or "Cancel" to update status
   - Changes update in real-time in Supabase

## Frontend Features

### Form Submission Flow
```
User fills form → Clicks "CONFIRM RESERVATION"
        ↓
Validation checks (all fields required)
        ↓
Insert to Supabase reservations table
        ↓
Form fields clear automatically
        ↓
Success message shows for 3 seconds
        ↓
User can make another reservation
```

### Gallery Display Flow
```
Page loads
        ↓
loadGalleryImages() fetches from Supabase
        ↓
If images found: Display from database
        ↓
If empty: Use beautiful default Unsplash images
        ↓
Images display in scrolling carousel
```

## Environment Variables

Already configured in Vercel project settings:
```
SUPABASE_URL=https://bjndvhjzcpnlwuvfhfnw.supabase.co
SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Debugging

### Check Browser Console
Open browser developer tools (F12) and check console for:
- `[v0] Loaded gallery images: X` - Confirms gallery loaded
- `[v0] Loaded reservations: X` - Confirms reservations loaded
- Error messages if Supabase connection fails

### Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| Images not showing | Check if `gallery_images` table has data; fallback uses default Unsplash |
| Booking not saving | Verify `reservations` table exists with correct schema |
| Admin dashboard empty | Check `reservations` table has records |
| Supabase client not loading | Verify CDN script link is correct |

## Future Enhancements (Phase 2)

### Email Notifications
- Requires Supabase Edge Functions or backend API
- Send confirmation email to customer after booking
- Send admin notification for new reservations
- Implementation: Use SendGrid or Resend API

### Additional Features
- Reservation modification/cancellation from customer email link
- Availability calendar blocking
- Payment processing integration
- SMS notifications
- User registration system

## Testing Checklist

- [x] Booking form submits to Supabase
- [x] Form fields clear after submission
- [x] Success message displays
- [x] Gallery images load from database
- [x] Gallery fallback to default images works
- [x] Admin can see reservations
- [x] Admin can confirm reservations
- [x] Admin can cancel reservations
- [x] Database updates reflect in UI

## Files Structure

```
/vercel/share/v0-project/
├── index.html                          # Main file with all integration
├── SUPABASE_INTEGRATION_GUIDE.md       # This file
└── README.md                           # Original project README
```

## Support & Documentation

- **Supabase Docs**: https://supabase.com/docs
- **Supabase Dashboard**: https://app.supabase.com/
- **Alpine.js Docs**: https://alpinejs.dev/
- **JavaScript Client Library**: https://github.com/supabase/supabase-js

## API Reference

### Insert Booking
```javascript
supabase.from('reservations').insert([{
  first_name: 'John',
  last_name: 'Smith',
  email: 'john@example.com',
  phone: '+44 7123 456789',
  date: '2025-07-25',
  time: '19:00',
  guests: '2 Guests',
  type: 'À la carte',
  notes: 'Special request...'
}])
```

### Fetch Gallery Images
```javascript
supabase.from('gallery_images').select('*').order('order', { ascending: true })
```

### Update Reservation Status
```javascript
supabase.from('reservations')
  .update({ status: 'confirmed' })
  .eq('email', 'john@example.com')
  .eq('date', '2025-07-25')
```

---

**Last Updated**: May 2026
**Status**: ✅ Complete and Tested
**Next Phase**: Email Notifications Integration
