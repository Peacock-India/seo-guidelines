# Performance Optimization

Page speed and Core Web Vitals are crucial ranking factors. This guide covers essential performance optimizations.

## Core Web Vitals

### Key Metrics

1. **Largest Contentful Paint (LCP)** - Loading performance
   - **Good**: ≤ 2.5 seconds
   - **Needs Improvement**: 2.5-4.0 seconds
   - **Poor**: > 4.0 seconds

2. **First Input Delay (FID)** - Interactivity
   - **Good**: ≤ 100 milliseconds
   - **Needs Improvement**: 100-300 milliseconds
   - **Poor**: > 300 milliseconds

3. **Cumulative Layout Shift (CLS)** - Visual stability
   - **Good**: ≤ 0.1
   - **Needs Improvement**: 0.1-0.25
   - **Poor**: > 0.25

## Performance Requirements

### Essential Benchmarks

- [ ] **Page Load Speed** < 3 seconds (tested with PageSpeed Insights)
- [ ] **Core Web Vitals** pass (LCP, FID, CLS)
- [ ] **Images optimized** (WebP/AVIF format, proper sizing)
- [ ] **Lazy loading** implemented for images below the fold
- [ ] **CSS/JS minified** and compressed
- [ ] **HTTPS enabled** with valid SSL certificate

## Image Optimization

### Modern Image Formats

```html
<!-- Use modern formats with fallbacks -->
<picture>
  <source srcset="/hero-image.avif" type="image/avif">
  <source srcset="/hero-image.webp" type="image/webp">
  <img src="/hero-image.jpg" alt="Hero image description" width="800" height="600">
</picture>
```

### Next.js Image Component

```typescript
import Image from 'next/image'

// Optimized image with Next.js
<Image
  src="/hero-image.jpg"
  alt="Descriptive alt text"
  width={800}
  height={600}
  priority // For above-the-fold images
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQ..."
/>

// For below-the-fold images
<Image
  src="/feature-image.jpg"
  alt="Feature description"
  width={400}
  height={300}
  loading="lazy"
/>
```

### Image Optimization Checklist

- [ ] **Compress images** (aim for <100KB per image)
- [ ] **Use appropriate dimensions** (don't scale down large images)
- [ ] **Implement lazy loading** for below-the-fold images
- [ ] **Specify width/height** to prevent layout shift
- [ ] **Use modern formats** (WebP, AVIF)
- [ ] **Optimize for different screen sizes** (responsive images)

## CSS Optimization

### Critical CSS

```html
<!-- Inline critical CSS -->
<style>
  /* Critical above-the-fold styles */
  body { font-family: -apple-system, BlinkMacSystemFont, sans-serif; }
  .hero { min-height: 100vh; display: flex; align-items: center; }
  .hero h1 { font-size: 3rem; font-weight: 700; }
</style>

<!-- Load non-critical CSS asynchronously -->
<link rel="preload" href="/styles/main.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="/styles/main.css"></noscript>
```

### CSS Best Practices

- [ ] **Minimize CSS** - Remove unused styles
- [ ] **Use CSS-in-JS** for component-specific styles
- [ ] **Avoid @import** - Use bundler imports instead
- [ ] **Optimize fonts** - Use font-display: swap
- [ ] **Reduce specificity** - Avoid deeply nested selectors

## JavaScript Optimization

### Code Splitting

```typescript
// Dynamic imports for code splitting
const LazyComponent = dynamic(() => import('./LazyComponent'), {
  loading: () => <div>Loading...</div>,
})

// Route-based code splitting
const AboutPage = dynamic(() => import('./pages/About'))
```

### Bundle Analysis

```bash
# Analyze bundle size
npm install --save-dev @next/bundle-analyzer

# Add to next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
})

module.exports = withBundleAnalyzer({
  // your next config
})

# Run analysis
ANALYZE=true npm run build
```

### JavaScript Best Practices

- [ ] **Tree shake unused code** - Use ES6 imports
- [ ] **Minimize JavaScript** - Use production builds
- [ ] **Defer non-critical scripts** - Use async/defer attributes
- [ ] **Optimize third-party scripts** - Load conditionally
- [ ] **Use service workers** - For caching strategies

## Font Optimization

### Google Fonts Optimization

```html
<!-- Preconnect to font origins -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

<!-- Load fonts with display swap -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
```

### Self-Hosted Fonts

```css
@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter-var.woff2') format('woff2');
  font-weight: 100 900;
  font-style: normal;
  font-display: swap;
}
```

### Font Loading Strategy

```typescript
// Next.js font optimization
import { Inter } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-inter',
})

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={inter.variable}>
      <body>{children}</body>
    </html>
  )
}
```

## Caching Strategies

### HTTP Caching Headers

```javascript
// next.config.js
module.exports = {
  async headers() {
    return [
      {
        source: '/images/:path*',
        headers: [
          {
            key: 'Cache-Control',
            value: 'public, max-age=31536000, immutable',
          },
        ],
      },
      {
        source: '/fonts/:path*',
        headers: [
          {
            key: 'Cache-Control',
            value: 'public, max-age=31536000, immutable',
          },
        ],
      },
    ]
  },
}
```

### Service Worker Caching

```javascript
// public/sw.js
self.addEventListener('fetch', (event) => {
  if (event.request.destination === 'image') {
    event.respondWith(
      caches.open('images').then((cache) => {
        return cache.match(event.request).then((response) => {
          return response || fetch(event.request).then((fetchResponse) => {
            cache.put(event.request, fetchResponse.clone())
            return fetchResponse
          })
        })
      })
    )
  }
})
```

## Performance Monitoring

### Core Web Vitals Tracking

```typescript
// utils/vitals.ts
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals'

function sendToAnalytics(metric) {
  // Send to your analytics service
  gtag('event', metric.name, {
    value: Math.round(metric.name === 'CLS' ? metric.value * 1000 : metric.value),
    event_label: metric.id,
    non_interaction: true,
  })
}

getCLS(sendToAnalytics)
getFID(sendToAnalytics)
getFCP(sendToAnalytics)
getLCP(sendToAnalytics)
getTTFB(sendToAnalytics)
```

### Performance Budget

```javascript
// next.config.js
module.exports = {
  experimental: {
    bundlePagesRouterDependencies: true,
  },
  // Set performance budgets
  webpack: (config, { dev, isServer }) => {
    if (!dev && !isServer) {
      config.optimization.splitChunks.cacheGroups.default.minSize = 20000
      config.optimization.splitChunks.cacheGroups.default.maxSize = 244000
    }
    return config
  },
}
```

## Testing Tools

### Essential Performance Tools

1. **Google PageSpeed Insights**
   - Tests real-world performance
   - Provides Core Web Vitals data
   - Offers optimization suggestions

2. **Lighthouse**
   - Comprehensive performance audit
   - Available in Chrome DevTools
   - CI/CD integration available

3. **WebPageTest**
   - Detailed waterfall analysis
   - Multiple location testing
   - Connection throttling

4. **Chrome DevTools**
   - Performance profiling
   - Network analysis
   - Coverage reports

### Automated Testing

```bash
# Install Lighthouse CI
npm install -g @lhci/cli

# Create lighthouserc.js
module.exports = {
  ci: {
    collect: {
      url: ['http://localhost:3000'],
      startServerCommand: 'npm start',
    },
    assert: {
      assertions: {
        'categories:performance': ['warn', { minScore: 0.9 }],
        'categories:accessibility': ['error', { minScore: 0.9 }],
      },
    },
  },
}

# Run tests
lhci autorun
```

## Performance Checklist

### Pre-Launch Performance Audit

- [ ] **PageSpeed Insights score** 90+ (mobile and desktop)
- [ ] **Core Web Vitals** all green
- [ ] **Images optimized** and properly sized
- [ ] **Fonts loaded efficiently** with font-display: swap
- [ ] **JavaScript bundles** analyzed and optimized
- [ ] **CSS critical path** optimized
- [ ] **Caching headers** configured
- [ ] **CDN configured** for static assets
- [ ] **Compression enabled** (gzip/brotli)
- [ ] **HTTP/2 enabled** on server

### Ongoing Monitoring

- [ ] **Real User Monitoring** (RUM) implemented
- [ ] **Performance budgets** set and monitored
- [ ] **Regular audits** scheduled
- [ ] **Performance regression** alerts configured
- [ ] **Third-party script** impact monitored