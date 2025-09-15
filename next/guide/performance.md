# Performance Optimization for Next.js

Complete guide for optimizing Next.js applications to achieve excellent Core Web Vitals scores and fast loading times using official Next.js app directory features.

## Core Web Vitals Targets

| Metric | Good | Needs Improvement | Poor | What it Measures |
|--------|------|-------------------|------|------------------|
| **LCP** (Largest Contentful Paint) | ≤ 2.5s | 2.5s - 4.0s | > 4.0s | Loading performance |
| **FID** (First Input Delay) | ≤ 100ms | 100ms - 300ms | > 300ms | Interactivity |
| **CLS** (Cumulative Layout Shift) | ≤ 0.1 | 0.1 - 0.25 | > 0.25 | Visual stability |
| **INP** (Interaction to Next Paint) | ≤ 200ms | 200ms - 500ms | > 500ms | Runtime responsiveness |

## Basic

Essential setup and core functionality that everyone needs.

### Image Optimization

```tsx
// app/page.tsx
import Image from 'next/image'
import ProfileImage from './profile.png' // Static import

export default function Page() {
  return (
    <div>
      {/* Above-the-fold image with priority */}
      <Image
        src="/hero.jpg"
        alt="Hero image"
        width={1200}
        height={600}
        priority
        placeholder="blur"
        blurDataURL="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQ..."
      />
      
      {/* Static import - automatically optimized */}
      <Image
        src={ProfileImage}
        alt="Profile picture"
        // width/height automatically provided
        placeholder="blur" // Optional blur-up while loading
      />
      
      {/* Below-the-fold image with lazy loading */}
      <Image
        src="/feature.jpg"
        alt="Feature description"
        width={800}
        height={400}
        loading="lazy"
      />
    </div>
  )
}
```

### Font Optimization

```tsx
// app/layout.tsx
import { Inter, Roboto_Mono } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-inter',
})

const robotoMono = Roboto_Mono({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-roboto-mono',
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={`${inter.variable} ${robotoMono.variable}`}>
      <body className={inter.className}>{children}</body>
    </html>
  )
}
```

### Web Vitals Monitoring

```tsx
// components/WebVitals.tsx
'use client'

import { useReportWebVitals } from 'next/web-vitals'

const logWebVitals = (metric: any) => {
  console.log(metric)
  
  // Send to analytics service
  if (window.gtag) {
    window.gtag('event', metric.name, {
      value: Math.round(
        metric.name === 'CLS' ? metric.value * 1000 : metric.value
      ),
      event_label: metric.id,
      non_interaction: true,
    })
  }
}

export function WebVitals() {
  useReportWebVitals(logWebVitals)
  return null
}
```

```tsx
// app/layout.tsx
import { WebVitals } from '@/components/WebVitals'

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <WebVitals />
        {children}
      </body>
    </html>
  )
}
```

### Basic Checklist

- [ ] Use Next.js `<Image>` component for all images
- [ ] Add `priority` prop to above-the-fold images
- [ ] Configure Google Fonts with `next/font/google`
- [ ] Set up Web Vitals monitoring with `useReportWebVitals`
- [ ] Specify image dimensions to prevent layout shift
- [ ] Use `display: 'swap'` for all fonts
- [ ] Enable automatic image optimization
- [ ] Test Core Web Vitals in production

## Advanced

Optional features and advanced configurations.

### Dynamic Imports & Code Splitting

```tsx
// app/page.tsx
import dynamic from 'next/dynamic'

// Dynamic import with loading state
const HeavyComponent = dynamic(() => import('@/components/HeavyComponent'), {
  loading: () => <div>Loading...</div>,
  ssr: false, // Disable server-side rendering if not needed
})

// Dynamic import for conditional rendering
const AdminPanel = dynamic(() => import('@/components/AdminPanel'), {
  ssr: false,
})

export default function Page() {
  return (
    <div>
      <h1>Main Content</h1>
      <HeavyComponent />
    </div>
  )
}
```

### Bundle Analysis Configuration

```javascript
// next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
})

/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    formats: ['image/webp', 'image/avif'],
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 's3.amazonaws.com',
        port: '',
        pathname: '/my-bucket/**',
      },
    ],
  },
  compress: true,
}

module.exports = withBundleAnalyzer(nextConfig)
```

### Advanced Web Vitals Handling

```tsx
// components/AdvancedWebVitals.tsx
'use client'

import { useReportWebVitals } from 'next/web-vitals'

type ReportWebVitalsCallback = Parameters<typeof useReportWebVitals>[0]

const handleWebVitals: ReportWebVitalsCallback = (metric) => {
  switch (metric.name) {
    case 'FCP': {
      // Handle First Contentful Paint
      console.log('FCP:', metric.value)
      break
    }
    case 'LCP': {
      // Handle Largest Contentful Paint
      console.log('LCP:', metric.value)
      break
    }
    case 'CLS': {
      // Handle Cumulative Layout Shift
      console.log('CLS:', metric.value)
      break
    }
    case 'FID': {
      // Handle First Input Delay
      console.log('FID:', metric.value)
      break
    }
    case 'INP': {
      // Handle Interaction to Next Paint
      console.log('INP:', metric.value)
      break
    }
    case 'TTFB': {
      // Handle Time to First Byte
      console.log('TTFB:', metric.value)
      break
    }
    default:
      console.log('Other metric:', metric.name, metric.value)
  }

  // Send to external analytics
  const body = JSON.stringify(metric)
  const url = 'https://your-analytics-endpoint.com'
  
  if (navigator.sendBeacon) {
    navigator.sendBeacon(url, body)
  } else {
    fetch(url, { body, method: 'POST', keepalive: true })
  }
}

export function AdvancedWebVitals() {
  useReportWebVitals(handleWebVitals)
  return null
}
```

### Local Font Configuration

```tsx
// app/layout.tsx
import localFont from 'next/font/local'

const customFont = localFont({
  src: [
    {
      path: './fonts/CustomFont-Regular.woff2',
      weight: '400',
      style: 'normal',
    },
    {
      path: './fonts/CustomFont-Bold.woff2',
      weight: '700',
      style: 'normal',
    },
  ],
  display: 'swap',
  variable: '--font-custom',
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={customFont.variable}>
      <body>{children}</body>
    </html>
  )
}
```

### Performance Headers Configuration

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
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

module.exports = nextConfig
```

### Advanced Checklist

- [ ] Set up bundle analyzer with `@next/bundle-analyzer`
- [ ] Configure dynamic imports for heavy components
- [ ] Implement advanced Web Vitals tracking with external analytics
- [ ] Set up local fonts with proper weight and style variants
- [ ] Configure static asset caching headers
- [ ] Enable WebP and AVIF image formats
- [ ] Test bundle size impact of new dependencies
- [ ] Monitor real user performance metrics

## References

- [Next.js Image Optimization](https://nextjs.org/docs/app/getting-started/images)
- [Next.js Font Optimization](https://nextjs.org/docs/app/getting-started/fonts)
- [Next.js useReportWebVitals Hook](https://nextjs.org/docs/app/api-reference/functions/use-report-web-vitals)
- [Next.js Production Checklist](https://nextjs.org/docs/app/guides/production-checklist)
- [Next.js Optimizations Overview](https://nextjs.org/docs/app/building-your-application/optimizing)
- [Core Web Vitals](https://web.dev/vitals/)
