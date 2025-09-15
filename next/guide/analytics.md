# Analytics & Tracking for Next.js

Complete guide for integrating Google Analytics 4 and Search Console with Next.js applications using official Vercel documentation and best practices.

## Basic

Essential setup and core functionality that everyone needs.

### Create GA4 Property

**Step 1: Set Up Google Analytics Account**
1. Go to [analytics.google.com](https://analytics.google.com)
2. Click **Start Measuring** (first time) or **Admin** → **Create** → **Account**
3. Enter account name and configure data-sharing settings
4. Click **Next**

**Step 2: Create GA4 Property**
1. Enter property name (e.g., "My Next.js Website")
2. Select reporting time zone and currency
3. Choose industry category and business size
4. Click **Create**

**Step 3: Add Web Data Stream**
1. Select **Web** platform
2. Enter website URL: `https://yourdomain.com`
3. Enter stream name: "Web Stream"
4. Enable **Enhanced Measurement** (recommended)
5. Click **Create stream**

**Step 4: Copy Measurement ID**
- Find your **Measurement ID** (format: `G-XXXXXXXXXX`) in the stream details
- Save this for Next.js integration

### Next.js Integration

```bash
npm install @next/third-parties@latest
```

```tsx
// app/layout.tsx - App Router
import { GoogleAnalytics } from '@next/third-parties/google'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
      {/* Only load in production */}
      {process.env.NODE_ENV === 'production' && (
        <GoogleAnalytics gaId={process.env.NEXT_PUBLIC_GA_ID} />
      )}
    </html>
  )
}
```

### Environment Variables

```bash
# .env.local
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
```

### Google Search Console Setup

```tsx
// app/layout.tsx
export const metadata = {
  verification: {
    google: 'your-verification-code',
  },
}
```

**Alternative DNS Verification:**
```txt
# Add TXT record to DNS
Name: @
Value: google-site-verification=your-verification-code
```

### Basic Checklist

- [ ] Create GA4 account and property at analytics.google.com
- [ ] Add web data stream and copy Measurement ID
- [ ] Enable Enhanced Measurement in GA4 dashboard
- [ ] Install `@next/third-parties` package
- [ ] Add `GoogleAnalytics` component to root layout
- [ ] Configure `NEXT_PUBLIC_GA_ID` environment variable
- [ ] Verify production-only loading works
- [ ] Add Google Search Console verification to metadata
- [ ] Submit sitemap to Search Console

## Advanced

Optional features and advanced configurations.

### Custom Event Tracking

```typescript
// lib/analytics.ts
export const trackEvent = (
  eventName: string,
  parameters?: Record<string, any>
) => {
  if (typeof window !== 'undefined' && window.gtag) {
    window.gtag('event', eventName, parameters)
  }
}

// GA4 event helpers
export const trackFormSubmit = (formName: string) => {
  trackEvent('form_submit', {
    form_name: formName,
    engagement_time_msec: Date.now()
  })
}

export const trackDownload = (fileName: string) => {
  trackEvent('file_download', {
    file_name: fileName,
    file_extension: fileName.split('.').pop()
  })
}
```

**Usage Example:**
```tsx
// components/ContactForm.tsx
import { trackFormSubmit } from '@/lib/analytics'

export default function ContactForm() {
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    trackFormSubmit('contact_form')
    // Handle form logic...
  }

  return <form onSubmit={handleSubmit}>/* Form fields */</form>
}
```

### Web Vitals Integration

```tsx
// app/layout.tsx
import { useReportWebVitals } from 'next/web-vitals'

export default function MyApp({ Component, pageProps }) {
  useReportWebVitals((metric) => {
    if (window.gtag) {
      window.gtag('event', metric.name, {
        value: Math.round(
          metric.name === 'CLS' ? metric.value * 1000 : metric.value
        ),
        event_label: metric.id,
        non_interaction: true,
      })
    }
  })

  return <Component {...pageProps} />
}
```

### Cookie Consent & GDPR

```tsx
// components/CookieConsent.tsx
'use client'

import { useState, useEffect } from 'react'

export default function CookieConsent() {
  const [showBanner, setShowBanner] = useState(false)

  useEffect(() => {
    const consent = localStorage.getItem('cookie-consent')
    if (!consent) setShowBanner(true)
  }, [])

  const acceptCookies = () => {
    localStorage.setItem('cookie-consent', 'accepted')
    
    if (window.gtag) {
      window.gtag('consent', 'update', {
        analytics_storage: 'granted',
        ad_storage: 'granted',
        functionality_storage: 'granted',
        personalization_storage: 'granted',
        security_storage: 'granted'
      })
    }
    
    setShowBanner(false)
  }

  if (!showBanner) return null

  return (
    <div className="fixed bottom-0 left-0 right-0 bg-gray-900 text-white p-4 z-50">
      <div className="max-w-4xl mx-auto flex items-center justify-between">
        <p className="text-sm">We use cookies to improve your experience.</p>
        <div className="flex gap-3">
          <button onClick={() => { localStorage.setItem('cookie-consent', 'declined'); setShowBanner(false) }}>
            Decline
          </button>
          <button onClick={acceptCookies}>Accept</button>
        </div>
      </div>
    </div>
  )
}
```

### Conditional Analytics Loading

```tsx
// app/layout.tsx
import { GoogleAnalytics } from '@next/third-parties/google'
import { cookies } from 'next/headers'

export default function RootLayout({ children }) {
  const cookieStore = cookies()
  const consent = cookieStore.get('cookie-consent')?.value
  const shouldLoadAnalytics = consent === 'accepted' && process.env.NODE_ENV === 'production'

  return (
    <html>
      <body>
        {children}
        <CookieConsent />
        {shouldLoadAnalytics && (
          <GoogleAnalytics gaId={process.env.NEXT_PUBLIC_GA_ID} />
        )}
      </body>
    </html>
  )
}
```

### TypeScript Definitions

```typescript
// types/gtag.d.ts
declare global {
  interface Window {
    gtag: (
      command: 'config' | 'event' | 'consent',
      targetId: string,
      config?: any
    ) => void
  }
}

export {}
```

### Development Testing

```typescript
// lib/analytics.ts
const isDev = process.env.NODE_ENV === 'development'

export const trackEvent = (eventName: string, parameters?: Record<string, any>) => {
  if (isDev) {
    console.log('Analytics Event:', { eventName, parameters })
    return
  }
  
  if (typeof window !== 'undefined' && window.gtag) {
    window.gtag('event', eventName, parameters)
  }
}
```

### Advanced Checklist

- [ ] Custom event tracking implemented for key interactions
- [ ] Web Vitals integration configured
- [ ] Cookie consent banner with Google Consent Mode
- [ ] Conditional analytics loading based on consent
- [ ] TypeScript definitions added
- [ ] Development testing setup with console logging
- [ ] GA4 DebugView testing completed
- [ ] Privacy policy updated for GDPR compliance
- [ ] Real-time reports monitoring configured

## References

- [Next.js Analytics Documentation](https://nextjs.org/docs/pages/guides/analytics)
- [Using Google Analytics with Next.js](https://nextjs.org/docs/messages/next-script-for-ga)
- [Google Analytics 4 Setup Guide](https://support.google.com/analytics/answer/9304153)
- [Google Search Console Verification](https://support.google.com/webmasters/answer/9008080)
- [Next.js Third-Party Libraries](https://nextjs.org/docs/app/guides/third-party-libraries)
- [Google Analytics Developer Documentation](https://developers.google.com/analytics/devguides/collection/ga4)