# Analytics & Tracking for Next.js

Complete guide for setting up Google Analytics 4 and Search Console integration with Next.js applications.

## Basic Setup (Required)

### Google Analytics 4 Installation

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

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        
        {process.env.NODE_ENV === 'production' && (
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

### Google Search Console Setup

#### HTML Meta Tag Verification
```typescript
// app/layout.tsx
export const metadata = {
  verification: {
    google: 'your-verification-code',
  },
}
```

#### DNS Verification Alternative
```txt
# Add TXT record to DNS
Name: @
Value: google-site-verification=your-verification-code
```

### Environment Variables

```bash
# .env.local
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
```

### Basic Setup Checklist
- [ ] GA4 property created
- [ ] Tracking code installed
- [ ] Page view tracking working
- [ ] Search Console verified
- [ ] Sitemap submitted
- [ ] Check Coverage report
- [ ] Monitor Performance report

## Advanced Features (Optional)

### Custom Event Tracking

#### Form Submissions
```typescript
// components/ContactForm.tsx
import { event } from '@/lib/gtag'

const handleSubmit = (e) => {
  e.preventDefault()
  
  event({
    action: 'submit',
    category: 'Contact',
    label: 'Contact Form',
  })
  
  // Handle form...
}
```

#### Click Tracking
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

### Scroll Depth Tracking

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

### Conversion Tracking

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

### Cookie Consent & GDPR

#### Cookie Consent Banner
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

#### Conditional Analytics Loading
```typescript
// app/layout.tsx
import { cookies } from 'next/headers'

export default function RootLayout({ children }) {
  const consent = cookies().get('cookie-consent')?.value
  const shouldLoadAnalytics = consent === 'accepted'
  
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

### Testing & Debugging

#### Debug Mode
```javascript
// Enable debug mode in browser console
window.gtag('config', 'G-XXXXXXXXXX', { debug_mode: true })
```

#### Verify Installation
```javascript
// Check if gtag is loaded
console.log(typeof window.gtag)

// Check dataLayer
console.log(window.dataLayer)
```

### Advanced Setup Checklist
- [ ] Custom events configured
- [ ] Conversion tracking setup
- [ ] Cookie consent implemented
- [ ] GDPR compliance verified
- [ ] Analytics tested in DebugView
- [ ] Privacy policy updated
- [ ] Real-time reports monitored

## Complete Testing Checklist

### Setup Verification
- [ ] GA4 property created and configured
- [ ] Tracking code installed correctly
- [ ] Environment-specific loading works
- [ ] Page view tracking functional
- [ ] Search Console verified
- [ ] Sitemap submitted and indexed

### Events & Conversions
- [ ] Form submissions tracked
- [ ] Key button clicks tracked
- [ ] Scroll depth tracking active
- [ ] Conversion goals configured
- [ ] Custom events firing correctly

### Privacy & Compliance
- [ ] Cookie consent banner implemented
- [ ] GDPR compliance verified
- [ ] Analytics respects user consent
- [ ] Privacy policy updated
- [ ] Data retention configured

### Performance & Monitoring
- [ ] Test in GA4 DebugView
- [ ] Verify no tracking on dev/staging
- [ ] Check all events in real-time
- [ ] Monitor Core Web Vitals
- [ ] Set up custom alerts

## References

- [Setup Google Analytics 4 and Next.js 14](https://www.nuctro.com/blog/setup-google-analytics-4-and-nextjs-14)
- [Google Analytics 4 Documentation](https://developers.google.com/analytics/devguides/collection/ga4)
- [Next.js Analytics Documentation](https://nextjs.org/docs/app/building-your-application/optimizing/analytics)
- [Google Search Console Help](https://support.google.com/webmasters)
- [GA4 Event Reference](https://developers.google.com/analytics/devguides/collection/ga4/reference/events)
- [Next.js Script Component](https://nextjs.org/docs/app/api-reference/components/script)
- [Google Tag Manager](https://developers.google.com/tag-platform/gtagjs)
- [GDPR Compliance Guide](https://gdpr.eu/cookies/)