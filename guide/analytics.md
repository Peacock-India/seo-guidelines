# Analytics & Tracking

Proper analytics setup is essential for measuring SEO performance and user behavior. This guide covers comprehensive tracking implementation.

## Google Analytics 4 (GA4)

### Basic Setup

```typescript
// lib/gtag.ts
export const GA_TRACKING_ID = process.env.NEXT_PUBLIC_GA_ID

// https://developers.google.com/analytics/devguides/collection/gtagjs/pages
export const pageview = (url: string) => {
  if (typeof window !== 'undefined' && GA_TRACKING_ID) {
    window.gtag('config', GA_TRACKING_ID, {
      page_path: url,
    })
  }
}

// https://developers.google.com/analytics/devguides/collection/gtagjs/events
export const event = ({ action, category, label, value }: {
  action: string
  category: string
  label?: string
  value?: number
}) => {
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

```typescript
// pages/_app.tsx
import { useEffect } from 'react'
import { useRouter } from 'next/router'
import Script from 'next/script'
import * as gtag from '../lib/gtag'

function MyApp({ Component, pageProps }) {
  const router = useRouter()

  useEffect(() => {
    const handleRouteChange = (url: string) => {
      gtag.pageview(url)
    }
    router.events.on('routeChangeComplete', handleRouteChange)
    return () => {
      router.events.off('routeChangeComplete', handleRouteChange)
    }
  }, [router.events])

  return (
    <>
      {/* Global Site Tag (gtag.js) - Google Analytics */}
      <Script
        strategy="afterInteractive"
        src={`https://www.googletagmanager.com/gtag/js?id=${gtag.GA_TRACKING_ID}`}
      />
      <Script
        id="gtag-init"
        strategy="afterInteractive"
        dangerouslySetInnerHTML={{
          __html: `
            window.dataLayer = window.dataLayer || [];
            function gtag(){dataLayer.push(arguments);}
            gtag('js', new Date());
            gtag('config', '${gtag.GA_TRACKING_ID}', {
              page_path: window.location.pathname,
            });
          `,
        }}
      />
      <Component {...pageProps} />
    </>
  )
}

export default MyApp
```

### Environment-Specific Configuration

```typescript
// lib/analytics.ts
const isProduction = process.env.NODE_ENV === 'production'
const isDevelopment = process.env.NODE_ENV === 'development'
const isSubdomain = typeof window !== 'undefined' && 
  window.location.hostname !== 'yourdomain.com'

export const shouldTrack = () => {
  // Only track in production on main domain
  return isProduction && !isSubdomain
}

export const initAnalytics = () => {
  if (!shouldTrack()) {
    console.log('Analytics disabled for this environment')
    return
  }

  // Initialize GA4
  if (typeof window !== 'undefined' && window.gtag) {
    window.gtag('config', process.env.NEXT_PUBLIC_GA_ID, {
      send_page_view: true,
      anonymize_ip: true,
    })
  }
}
```

## Google Search Console

### Verification Methods

#### HTML Meta Tag Method

```html
<!-- Add to <head> section -->
<meta name="google-site-verification" content="your-verification-code" />
```

#### Next.js Implementation

```typescript
// pages/_document.tsx
import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html>
      <Head>
        <meta 
          name="google-site-verification" 
          content={process.env.NEXT_PUBLIC_GSC_VERIFICATION} 
        />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

#### DNS Verification

```bash
# Add TXT record to your domain DNS
# Name: @
# Value: google-site-verification=your-verification-code
```

### Search Console Setup Checklist

- [ ] **Property verified** for main domain
- [ ] **Sitemap submitted** (XML sitemap URL)
- [ ] **URL inspection** tested for key pages
- [ ] **Coverage report** reviewed for errors
- [ ] **Performance report** monitored for rankings
- [ ] **Mobile usability** issues resolved
- [ ] **Core Web Vitals** report reviewed

## Event Tracking

### Custom Events

```typescript
// components/ContactForm.tsx
import { event } from '../lib/gtag'

const ContactForm = () => {
  const handleSubmit = (e) => {
    e.preventDefault()
    
    // Track form submission
    event({
      action: 'submit',
      category: 'Contact',
      label: 'Contact Form',
    })
    
    // Handle form submission
  }

  return (
    <form onSubmit={handleSubmit}>
      {/* form fields */}
    </form>
  )
}
```

### Scroll Tracking

```typescript
// hooks/useScrollTracking.ts
import { useEffect } from 'react'
import { event } from '../lib/gtag'

export const useScrollTracking = () => {
  useEffect(() => {
    let scrollDepth = 0
    const trackingPoints = [25, 50, 75, 100]
    
    const handleScroll = () => {
      const scrollPercent = Math.round(
        (window.scrollY / (document.body.scrollHeight - window.innerHeight)) * 100
      )
      
      trackingPoints.forEach(point => {
        if (scrollPercent >= point && scrollDepth < point) {
          scrollDepth = point
          event({
            action: 'scroll',
            category: 'Engagement',
            label: `${point}%`,
            value: point,
          })
        }
      })
    }
    
    window.addEventListener('scroll', handleScroll)
    return () => window.removeEventListener('scroll', handleScroll)
  }, [])
}
```

### Click Tracking

```typescript
// components/TrackableLink.tsx
import Link from 'next/link'
import { event } from '../lib/gtag'

interface TrackableLinkProps {
  href: string
  children: React.ReactNode
  category?: string
  label?: string
}

const TrackableLink = ({ 
  href, 
  children, 
  category = 'Link', 
  label 
}: TrackableLinkProps) => {
  const handleClick = () => {
    event({
      action: 'click',
      category,
      label: label || href,
    })
  }

  return (
    <Link href={href} onClick={handleClick}>
      {children}
    </Link>
  )
}

export default TrackableLink
```

## Performance Tracking

### Core Web Vitals

```typescript
// lib/webVitals.ts
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals'
import { event } from './gtag'

function sendToGoogleAnalytics({ name, delta, value, id }) {
  event({
    action: name,
    category: 'Web Vitals',
    label: id,
    value: Math.round(name === 'CLS' ? delta * 1000 : delta),
  })
}

export function reportWebVitals() {
  getCLS(sendToGoogleAnalytics)
  getFID(sendToGoogleAnalytics)
  getFCP(sendToGoogleAnalytics)
  getLCP(sendToGoogleAnalytics)
  getTTFB(sendToGoogleAnalytics)
}
```

### Page Load Timing

```typescript
// lib/performanceTracking.ts
export const trackPageLoad = () => {
  if (typeof window !== 'undefined' && window.performance) {
    window.addEventListener('load', () => {
      setTimeout(() => {
        const perfData = window.performance.timing
        const pageLoadTime = perfData.loadEventEnd - perfData.navigationStart
        const domReadyTime = perfData.domContentLoadedEventEnd - perfData.navigationStart
        
        event({
          action: 'timing_complete',
          category: 'Performance',
          label: 'Page Load Time',
          value: Math.round(pageLoadTime),
        })
        
        event({
          action: 'timing_complete',
          category: 'Performance',
          label: 'DOM Ready Time',
          value: Math.round(domReadyTime),
        })
      }, 0)
    })
  }
}
```

## Error Tracking

### JavaScript Error Tracking

```typescript
// lib/errorTracking.ts
export const initErrorTracking = () => {
  if (typeof window !== 'undefined') {
    window.addEventListener('error', (e) => {
      event({
        action: 'javascript_error',
        category: 'Error',
        label: `${e.filename}:${e.lineno}:${e.colno}`,
      })
    })
    
    window.addEventListener('unhandledrejection', (e) => {
      event({
        action: 'promise_rejection',
        category: 'Error',
        label: e.reason?.toString() || 'Unknown promise rejection',
      })
    })
  }
}
```

### 404 Error Tracking

```typescript
// pages/404.tsx
import { useEffect } from 'react'
import { useRouter } from 'next/router'
import { event } from '../lib/gtag'

export default function Custom404() {
  const router = useRouter()
  
  useEffect(() => {
    event({
      action: '404',
      category: 'Error',
      label: router.asPath,
    })
  }, [router.asPath])
  
  return (
    <div>
      <h1>404 - Page Not Found</h1>
      <p>The page you're looking for doesn't exist.</p>
    </div>
  )
}
```

## Conversion Tracking

### Goal Setup

```typescript
// lib/conversions.ts
export const trackConversion = (conversionType: string, value?: number) => {
  event({
    action: 'conversion',
    category: 'Goal',
    label: conversionType,
    value,
  })
  
  // Send to Google Ads if applicable
  if (typeof window !== 'undefined' && window.gtag) {
    window.gtag('event', 'conversion', {
      send_to: 'AW-CONVERSION_ID/CONVERSION_LABEL',
      value: value,
      currency: 'USD',
    })
  }
}

// Usage examples
export const trackFormSubmission = () => {
  trackConversion('form_submission')
}

export const trackNewsletterSignup = () => {
  trackConversion('newsletter_signup')
}

export const trackDownload = (filename: string) => {
  event({
    action: 'download',
    category: 'Engagement',
    label: filename,
  })
}
```

## Privacy & GDPR Compliance

### Cookie Consent

```typescript
// components/CookieConsent.tsx
import { useState, useEffect } from 'react'
import { initAnalytics } from '../lib/analytics'

const CookieConsent = () => {
  const [showConsent, setShowConsent] = useState(false)
  
  useEffect(() => {
    const consent = localStorage.getItem('cookie-consent')
    if (!consent) {
      setShowConsent(true)
    } else if (consent === 'accepted') {
      initAnalytics()
    }
  }, [])
  
  const acceptCookies = () => {
    localStorage.setItem('cookie-consent', 'accepted')
    setShowConsent(false)
    initAnalytics()
  }
  
  const declineCookies = () => {
    localStorage.setItem('cookie-consent', 'declined')
    setShowConsent(false)
  }
  
  if (!showConsent) return null
  
  return (
    <div className="cookie-consent">
      <p>We use cookies to improve your experience and analyze site usage.</p>
      <button onClick={acceptCookies}>Accept</button>
      <button onClick={declineCookies}>Decline</button>
    </div>
  )
}

export default CookieConsent
```

### Data Anonymization

```typescript
// lib/privacy.ts
export const configurePrivacySettings = () => {
  if (typeof window !== 'undefined' && window.gtag) {
    // Anonymize IP addresses
    window.gtag('config', process.env.NEXT_PUBLIC_GA_ID, {
      anonymize_ip: true,
      allow_google_signals: false,
      allow_ad_personalization_signals: false,
    })
  }
}
```

## Monitoring & Alerts

### Custom Dashboards

```typescript
// lib/monitoring.ts
export const trackBusinessMetrics = () => {
  // Track key business metrics
  const metrics = {
    page_views: 'pageview',
    user_engagement: 'engagement',
    conversion_rate: 'conversion',
    bounce_rate: 'bounce',
  }
  
  Object.entries(metrics).forEach(([metric, eventName]) => {
    // Send custom metrics to analytics
    event({
      action: eventName,
      category: 'Business Metrics',
      label: metric,
    })
  })
}
```

### Alert Configuration

```javascript
// Google Analytics Intelligence API alerts
const alertConfig = {
  traffic_drop: {
    metric: 'sessions',
    threshold: -20, // 20% decrease
    period: '7d',
  },
  conversion_drop: {
    metric: 'goal_completions',
    threshold: -15, // 15% decrease
    period: '24h',
  },
  page_speed_degradation: {
    metric: 'avg_page_load_time',
    threshold: 3000, // 3 seconds
    period: '1d',
  },
}
```

## Analytics Checklist

### Setup Requirements

- [ ] **Google Analytics 4** configured and tracking
- [ ] **Google Search Console** verified and monitoring
- [ ] **Environment-specific tracking** (production only)
- [ ] **Custom events** implemented for key actions
- [ ] **Core Web Vitals** tracking enabled
- [ ] **Error tracking** configured
- [ ] **Conversion goals** defined and tracked
- [ ] **Cookie consent** implemented (GDPR compliance)
- [ ] **Data anonymization** enabled
- [ ] **Custom dashboards** created for key metrics

### Ongoing Monitoring

- [ ] **Weekly performance** reports reviewed
- [ ] **Search Console** errors monitored
- [ ] **Conversion rates** tracked and optimized
- [ ] **User behavior** analyzed for improvements
- [ ] **Technical SEO** issues identified and resolved
- [ ] **Content performance** measured and optimized
- [ ] **Mobile vs desktop** performance compared
- [ ] **Page speed** trends monitored

### Reporting Schedule

- **Daily**: Traffic, conversions, errors
- **Weekly**: Detailed performance analysis
- **Monthly**: Comprehensive SEO and business metrics review
- **Quarterly**: Strategy review and optimization planning