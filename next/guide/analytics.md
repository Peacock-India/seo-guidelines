# Analytics & Tracking

## Google Analytics 4 Setup

### Basic Installation

```typescript
// lib/gtag.ts
export const GA_TRACKING_ID = process.env.NEXT_PUBLIC_GA_ID

export const pageview = (url: string) => {
  if (typeof window !== 'undefined' && GA_TRACKING_ID) {
    window.gtag('config', GA_TRACKING_ID, {
      page_path: url,
    })
  }
}

export const event = ({ action, category, label, value }) => {
  if (typeof window !== 'undefined' && GA_TRACKING_ID) {
    window.gtag('event', action, {
      event_category: category,
      event_label: label,
      value: value,
    })
  }
}
```

### Next.js Integration

```tsx
// app/layout.tsx
import Script from 'next/script'
import { getSEOConfig } from '@/utils/seo-config'

export default function RootLayout({ children }) {
  const seoConfig = getSEOConfig()
  
  return (
    <html>
      <body>
        {children}
        
        {/* Only load on production main domain */}
        {seoConfig.includeAnalytics && (
          <>
            <Script
              src={`https://www.googletagmanager.com/gtag/js?id=${process.env.NEXT_PUBLIC_GA_ID}`}
              strategy="afterInteractive"
            />
            <Script id="google-analytics" strategy="afterInteractive">
              {`
                window.dataLayer = window.dataLayer || [];
                function gtag(){dataLayer.push(arguments);}
                gtag('js', new Date());
                gtag('config', '${process.env.NEXT_PUBLIC_GA_ID}');
              `}
            </Script>
          </>
        )}
      </body>
    </html>
  )
}
```

### Page View Tracking

```typescript
// app/providers.tsx
'use client'

import { useEffect } from 'react'
import { usePathname, useSearchParams } from 'next/navigation'
import * as gtag from '@/lib/gtag'

export function Analytics() {
  const pathname = usePathname()
  const searchParams = useSearchParams()

  useEffect(() => {
    if (pathname) {
      gtag.pageview(pathname + searchParams.toString())
    }
  }, [pathname, searchParams])

  return null
}
```

## Google Search Console

### Verification Methods

#### 1. HTML Meta Tag
```html
<meta name="google-site-verification" content="your-verification-code" />
```

#### 2. Next.js Implementation
```typescript
// app/layout.tsx
export const metadata = {
  verification: {
    google: 'your-verification-code',
  },
}
```

#### 3. DNS Verification
```txt
# Add TXT record to DNS
Name: @
Value: google-site-verification=your-verification-code
```

### Setup Checklist
- [ ] Property verified
- [ ] Sitemap submitted
- [ ] Check Coverage report
- [ ] Monitor Performance report
- [ ] Review Core Web Vitals

## Event Tracking

### Form Submissions

```typescript
// components/ContactForm.tsx
import { event } from '@/lib/gtag'

const handleSubmit = (e) => {
  e.preventDefault()
  
  // Track form submission
  event({
    action: 'submit',
    category: 'Contact',
    label: 'Contact Form',
  })
  
  // Handle form...
}
```

### Click Tracking

```typescript
// components/TrackableButton.tsx
import { event } from '@/lib/gtag'

const TrackableButton = ({ children, eventLabel }) => {
  const handleClick = () => {
    event({
      action: 'click',
      category: 'Button',
      label: eventLabel,
    })
  }

  return (
    <button onClick={handleClick}>
      {children}
    </button>
  )
}
```

### Scroll Tracking

```typescript
// hooks/useScrollTracking.ts
import { useEffect } from 'react'
import { event } from '@/lib/gtag'

export const useScrollTracking = () => {
  useEffect(() => {
    let scrolled = { 25: false, 50: false, 75: false, 100: false }
    
    const handleScroll = () => {
      const scrollPercent = Math.round(
        (window.scrollY / (document.body.scrollHeight - window.innerHeight)) * 100
      )
      
      Object.entries(scrolled).forEach(([threshold, tracked]) => {
        if (scrollPercent >= Number(threshold) && !tracked) {
          scrolled[threshold] = true
          event({
            action: 'scroll',
            category: 'Engagement',
            label: `${threshold}%`,
          })
        }
      })
    }
    
    window.addEventListener('scroll', handleScroll)
    return () => window.removeEventListener('scroll', handleScroll)
  }, [])
}
```

## Conversion Tracking

### Goal Events

```typescript
// lib/conversions.ts
import { event } from './gtag'

export const trackFormSubmit = () => {
  event({
    action: 'generate_lead',
    category: 'conversion',
    label: 'form_submission',
  })
}

export const trackSignup = () => {
  event({
    action: 'sign_up',
    category: 'conversion',
    label: 'newsletter',
  })
}

export const trackDownload = (filename: string) => {
  event({
    action: 'file_download',
    category: 'engagement',
    label: filename,
  })
}
```

## Privacy & GDPR

### Cookie Consent Banner

```typescript
// components/CookieConsent.tsx
'use client'

import { useState, useEffect } from 'react'

export default function CookieConsent() {
  const [showBanner, setShowBanner] = useState(false)
  
  useEffect(() => {
    const consent = localStorage.getItem('cookie-consent')
    if (!consent) {
      setShowBanner(true)
    }
  }, [])
  
  const acceptCookies = () => {
    localStorage.setItem('cookie-consent', 'accepted')
    setShowBanner(false)
    // Load analytics scripts
    window.location.reload()
  }
  
  const declineCookies = () => {
    localStorage.setItem('cookie-consent', 'declined')
    setShowBanner(false)
  }
  
  if (!showBanner) return null
  
  return (
    <div className="cookie-banner">
      <p>We use cookies to improve your experience.</p>
      <button onClick={acceptCookies}>Accept</button>
      <button onClick={declineCookies}>Decline</button>
    </div>
  )
}
```

### Conditional Analytics Loading

```typescript
// app/layout.tsx
export default function RootLayout({ children }) {
  const consent = cookies().get('cookie-consent')?.value
  const shouldLoadAnalytics = consent === 'accepted' && getSEOConfig().includeAnalytics
  
  return (
    <html>
      <body>
        {children}
        <CookieConsent />
        {shouldLoadAnalytics && <GoogleAnalytics />}
      </body>
    </html>
  )
}
```

## Environment Variables

```bash
# .env.local
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
NEXT_PUBLIC_DOMAIN=www.yourdomain.com
```

## Testing Analytics

### Debug Mode

```javascript
// Enable debug mode in browser console
window.gtag('config', 'G-XXXXXXXXXX', { debug_mode: true })
```

### Google Analytics Debugger
1. Install Chrome extension
2. Enable debugger
3. Check console for events

### Verify Installation
```javascript
// Check if gtag is loaded
console.log(typeof window.gtag)

// Check dataLayer
console.log(window.dataLayer)
```

---

## Analytics Checklist

### Setup
- [ ] GA4 property created
- [ ] Tracking code installed
- [ ] Environment-specific loading
- [ ] Page view tracking working
- [ ] Search Console verified
- [ ] Sitemap submitted

### Events
- [ ] Form submissions tracked
- [ ] Key clicks tracked
- [ ] Scroll depth tracked
- [ ] Conversions configured

### Privacy
- [ ] Cookie consent banner
- [ ] GDPR compliant
- [ ] Analytics honors consent
- [ ] Privacy policy updated

### Testing
- [ ] Test in GA4 DebugView
- [ ] Verify no tracking on dev/staging
- [ ] Check all events firing
- [ ] Monitor real-time reports
