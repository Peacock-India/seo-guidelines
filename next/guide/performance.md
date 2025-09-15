# Performance Optimization

## Core Web Vitals Targets

| Metric | Good | Poor | What it Measures |
|--------|------|------|------------------|
| **LCP** (Largest Contentful Paint) | ≤ 2.5s | > 4.0s | Loading performance |
| **FID** (First Input Delay) | ≤ 100ms | > 300ms | Interactivity |
| **CLS** (Cumulative Layout Shift) | ≤ 0.1 | > 0.25 | Visual stability |

## Essential Performance Checklist

- [ ] Page loads in under 3 seconds
- [ ] PageSpeed Insights score 90+
- [ ] All Core Web Vitals green
- [ ] Images optimized and lazy loaded
- [ ] CSS/JS minified
- [ ] HTTPS enabled

## Image Optimization

### Next.js Image Component

```jsx
import Image from 'next/image'

// Above-the-fold image
<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1200}
  height={600}
  priority
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,..."
/>

// Below-the-fold image
<Image
  src="/feature.jpg"
  alt="Feature description"
  width={800}
  height={400}
  loading="lazy"
/>
```

### Modern Format with Fallback

```html
<picture>
  <source srcset="/image.avif" type="image/avif">
  <source srcset="/image.webp" type="image/webp">
  <img src="/image.jpg" alt="Description" width="800" height="600">
</picture>
```

### Image Rules
- Keep under 100KB per image
- Use WebP/AVIF formats
- Specify width/height (prevents layout shift)
- Lazy load below-fold images
- Use descriptive file names

## CSS Optimization

### Critical CSS (Inline)

```html
<head>
  <style>
    /* Critical above-the-fold styles */
    body { font-family: system-ui, sans-serif; }
    .hero { min-height: 100vh; display: flex; }
  </style>
  
  <!-- Load non-critical CSS async -->
  <link rel="preload" href="/styles.css" as="style" 
        onload="this.onload=null;this.rel='stylesheet'">
</head>
```

### CSS Best Practices
- Remove unused CSS
- Avoid @import statements
- Minimize CSS files
- Use CSS-in-JS for components

## JavaScript Optimization

### Code Splitting

```jsx
// Dynamic imports
import dynamic from 'next/dynamic'

const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <div>Loading...</div>,
})

// Route-based splitting
const AboutPage = dynamic(() => import('./pages/About'))
```

### Bundle Analysis

```bash
# Install analyzer
npm install @next/bundle-analyzer

# next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
})

module.exports = withBundleAnalyzer({})

# Run analysis
ANALYZE=true npm run build
```

## Font Optimization

### Next.js Font Loading

```jsx
// app/layout.tsx
import { Inter } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-inter',
})

export default function RootLayout({ children }) {
  return (
    <html className={inter.variable}>
      <body>{children}</body>
    </html>
  )
}
```

### Self-Hosted Fonts

```css
@font-face {
  font-family: 'CustomFont';
  src: url('/fonts/font.woff2') format('woff2');
  font-display: swap;
}
```

## Caching

### Static Assets

```javascript
// next.config.js
module.exports = {
  async headers() {
    return [
      {
        source: '/images/:path*',
        headers: [{
          key: 'Cache-Control',
          value: 'public, max-age=31536000, immutable',
        }],
      },
      {
        source: '/fonts/:path*',
        headers: [{
          key: 'Cache-Control',
          value: 'public, max-age=31536000, immutable',
        }],
      },
    ]
  },
}
```

## Performance Monitoring

### Web Vitals Tracking

```jsx
// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <WebVitals />
      </body>
    </html>
  )
}

// components/WebVitals.tsx
'use client'

import { useReportWebVitals } from 'next/web-vitals'

export function WebVitals() {
  useReportWebVitals((metric) => {
    // Send to analytics
    console.log(metric)
  })
  
  return null
}
```

## Testing Tools

### 1. Google PageSpeed Insights
- Test URL: https://pagespeed.web.dev
- Shows real-world data
- Provides optimization suggestions

### 2. Lighthouse (Chrome DevTools)
```bash
# Command line
npm install -g lighthouse
lighthouse https://yourdomain.com
```

### 3. WebPageTest
- Detailed waterfall analysis
- Test from multiple locations

### 4. Core Web Vitals Testing
```javascript
// Quick test in console
new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    console.log(entry.name, entry.value)
  }
}).observe({ entryTypes: ['largest-contentful-paint', 'layout-shift', 'first-input'] })
```

## Quick Wins

### 1. Preconnect to External Domains
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
```

### 2. Resource Hints
```html
<!-- Preload critical resources -->
<link rel="preload" href="/font.woff2" as="font" crossorigin>

<!-- Prefetch next page -->
<link rel="prefetch" href="/about">
```

### 3. Compression
```javascript
// next.config.js
module.exports = {
  compress: true,
}
```

---

## Performance Checklist

### Before Launch
- [ ] PageSpeed score 90+ (mobile & desktop)
- [ ] All images optimized (< 100KB)
- [ ] Fonts using display: swap
- [ ] JavaScript bundles analyzed
- [ ] Critical CSS inlined
- [ ] Caching headers set
- [ ] Compression enabled

### Core Web Vitals
- [ ] LCP ≤ 2.5 seconds
- [ ] FID ≤ 100ms
- [ ] CLS ≤ 0.1

### Monitoring
- [ ] Web Vitals tracking setup
- [ ] Performance budgets set
- [ ] Regular audits scheduled
