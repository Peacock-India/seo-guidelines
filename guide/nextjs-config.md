# Next.js SEO Configuration

## Production Layout (Main Domain)

```typescript
// app/layout.tsx
import { Metadata } from 'next'

const SITE_URL = process.env.NEXT_PUBLIC_SITE_URL || "https://www.yourdomain.com"
const SITE_NAME = "Your Brand Name"

export const metadata: Metadata = {
  metadataBase: new URL(SITE_URL),
  
  // SEO Control
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      'max-video-preview': -1,
      'max-image-preview': 'large',
      'max-snippet': -1,
    },
  },
  
  // Basic Info
  title: {
    default: "Your Landing Page Title - Brand Name",
    template: "%s | Your Brand Name"
  },
  description: "Compelling meta description under 160 characters with target keywords",
  keywords: ["primary keyword", "secondary keyword", "brand name"],
  authors: [{ name: "Your Name" }],
  creator: "Your Name",
  publisher: "Your Brand Name",
  
  // Canonical
  alternates: {
    canonical: SITE_URL,
  },
  
  // Open Graph
  openGraph: {
    type: "website",
    locale: "en_US",
    url: SITE_URL,
    siteName: SITE_NAME,
    title: "Social Media Title - Under 60 Characters",
    description: "Social media description - under 120 characters",
    images: [{
      url: `${SITE_URL}/og-image.jpg`,
      width: 1200,
      height: 630,
      alt: "Your brand description",
    }],
  },
  
  // Twitter
  twitter: {
    card: "summary_large_image",
    site: "@yourhandle",
    creator: "@yourhandle",
    title: "Twitter Title - Under 70 Characters",
    description: "Twitter description - under 120 characters",
    images: [`${SITE_URL}/twitter-image.jpg`],
  },
  
  // Verification
  verification: {
    google: "your-google-verification-code",
  },
}
```

## Blocked Domain Layout

```typescript
// app/layout.tsx (Staging/Dev)
export const metadata: Metadata = {
  robots: {
    index: false,
    follow: false,
    googleBot: {
      index: false,
      follow: false,
    },
  },
  title: "Development Site - Not for Public",
  description: "This is a development environment",
}
```

## Dynamic Metadata with Environment Detection

```typescript
// app/layout.tsx with environment detection
import { getSEOConfig } from '@/utils/seo-config'

const seoConfig = getSEOConfig()

export const metadata: Metadata = {
  ...(seoConfig.index ? {
    // Production metadata
    title: "Your Title",
    description: "Your description",
    openGraph: { /* ... */ },
    twitter: { /* ... */ },
  } : {
    // Blocked metadata
    title: "Dev Site",
    description: "Not for public",
  }),
  
  robots: {
    index: seoConfig.index,
    follow: seoConfig.follow,
  },
}
```

## Headers Configuration

### Method 1: next.config.js

```javascript
// next.config.js
module.exports = {
  async headers() {
    const headers = []
    
    // Block preview deployments
    if (process.env.VERCEL_ENV === 'preview' || 
        process.env.NODE_ENV !== 'production') {
      headers.push({
        source: '/(.*)',
        headers: [{
          key: 'X-Robots-Tag',
          value: 'noindex, nofollow, noarchive',
        }],
      })
    }
    
    return headers
  },
}
```

### Method 2: Middleware

```typescript
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  const response = NextResponse.next()
  const hostname = request.headers.get('host') || ''
  
  // Block non-production domains
  if (hostname.includes('vercel.app') || 
      hostname.includes('netlify.app') ||
      hostname.includes('staging.')) {
    response.headers.set('X-Robots-Tag', 'noindex, nofollow')
  }
  
  return response
}
```

## Dynamic Sitemap

```typescript
// app/sitemap.ts
import { MetadataRoute } from 'next'

export default function sitemap(): MetadataRoute.Sitemap {
  const baseUrl = 'https://www.yourdomain.com'
  
  return [
    {
      url: baseUrl,
      lastModified: new Date(),
      changeFrequency: 'weekly',
      priority: 1,
    },
    {
      url: `${baseUrl}/about`,
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.8,
    },
    {
      url: `${baseUrl}/services`,
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.8,
    },
    {
      url: `${baseUrl}/contact`,
      lastModified: new Date(),
      changeFrequency: 'yearly',
      priority: 0.5,
    },
  ]
}
```

## Dynamic Robots.txt

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'
import { getSEOConfig } from '@/utils/seo-config'

export default function robots(): MetadataRoute.Robots {
  const seoConfig = getSEOConfig()
  const baseUrl = 'https://www.yourdomain.com'
  
  if (seoConfig.index) {
    // Production
    return {
      rules: {
        userAgent: '*',
        allow: '/',
        disallow: ['/admin/', '/api/', '/private/'],
      },
      sitemap: `${baseUrl}/sitemap.xml`,
    }
  }
  
  // Blocked domains
  return {
    rules: {
      userAgent: '*',
      disallow: '/',
    },
  }
}
```

## Environment Variables

```bash
# .env.local
NEXT_PUBLIC_SITE_URL=https://www.yourdomain.com
NEXT_PUBLIC_SITE_NAME="Your Brand Name"
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
```

## Vercel Configuration

```json
// vercel.json
{
  "headers": [
    {
      "source": "/(.*)",
      "has": [{
        "type": "host",
        "value": "*.vercel.app"
      }],
      "headers": [{
        "key": "X-Robots-Tag",
        "value": "noindex, nofollow"
      }]
    }
  ]
}
```

## Analytics Integration

```typescript
// app/layout.tsx
import Script from 'next/script'
import { getSEOConfig } from '@/utils/seo-config'

export default function RootLayout({ children }) {
  const seoConfig = getSEOConfig()
  
  return (
    <html lang="en">
      <body>
        {children}
        
        {/* Only load analytics on production */}
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

---

## Quick Setup Checklist

- [ ] Configure metadata in `layout.tsx`
- [ ] Set up environment detection in `seo-config.ts`
- [ ] Add headers to `next.config.js`
- [ ] Create dynamic `sitemap.ts`
- [ ] Create dynamic `robots.ts`
- [ ] Configure environment variables
- [ ] Add analytics (production only)
- [ ] Test with different domains
