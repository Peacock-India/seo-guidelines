# Next.js SEO Configuration

Comprehensive Next.js setup for proper SEO implementation with environment-specific controls.

## Production Domain Layout

```typescript
// app/layout.tsx - Production Domain
const SITE_URL = process.env.NEXT_PUBLIC_SITE_URL || "https://www.yourdomain.com";
const SITE_NAME = "Your Brand Name";

export const metadata: Metadata = {
  metadataBase: new URL(SITE_URL),
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
  title: {
    default: "Your Landing Page Title - Brand Name",
    template: "%s | Your Brand Name"
  },
  description: "Compelling meta description under 160 characters with target keywords",
  keywords: ["primary keyword", "secondary keyword", "brand name"],
  authors: [{ name: "Your Name", url: SITE_URL }],
  creator: "Your Name",
  publisher: "Your Brand Name",
  
  alternates: {
    canonical: SITE_URL,
  },
  
  openGraph: {
    type: "website",
    locale: "en_US",
    url: SITE_URL,
    siteName: SITE_NAME,
    title: "Social Media Title - Keep Under 60 Characters",
    description: "Social media description - keep under 120 characters",
    images: [
      {
        url: `${SITE_URL}/og-image.jpg`,
        width: 1200,
        height: 630,
        alt: "Your brand social media image description",
        type: "image/jpeg",
      },
    ],
  },
  
  twitter: {
    card: "summary_large_image",
    site: "@yourtwitterhandle",
    creator: "@yourtwitterhandle",
    title: "Twitter Title - Keep Under 70 Characters",
    description: "Twitter description - keep under 120 characters",
    images: [`${SITE_URL}/twitter-image.jpg`],
  },
  
  verification: {
    google: "your-google-verification-code",
    // yandex: "your-yandex-verification-code",
    // bing: "your-bing-verification-code",
  },
};
```

## Subdomain/Development Layout

```typescript
// app/layout.tsx - Staging/Development Domain
const SITE_URL = process.env.NEXT_PUBLIC_SITE_URL || "https://staging.yourdomain.com";

export const metadata: Metadata = {
  robots: {
    index: false,
    follow: false,
    googleBot: {
      index: false,
      follow: false,
    },
  },
  title: "Development Site - Not for Public Access",
  description: "This is a development environment",
  
  // No OpenGraph, Twitter, or other social metadata for blocked domains
};
```

## Headers Configuration

### Method 1: next.config.js (Recommended)

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  async headers() {
    const headers = [];
    
    // Block preview deployments from being indexed
    if (
      process.env.NEXT_PUBLIC_VERCEL_ENV === 'preview' ||
      process.env.CONTEXT === 'deploy-preview' || // Netlify
      process.env.NODE_ENV !== 'production'
    ) {
      headers.push({
        source: '/(.*)',
        headers: [
          {
            key: 'X-Robots-Tag',
            value: 'noindex, nofollow, noarchive, nosnippet',
          },
        ],
      });
    }
    
    // Block specific domains
    const blockedDomains = [
      'staging.yourdomain.com',
      'dev.yourdomain.com', 
      'test.yourdomain.com'
    ];
    
    blockedDomains.forEach(domain => {
      headers.push({
        source: '/(.*)',
        has: [
          {
            type: 'host',
            value: domain,
          },
        ],
        headers: [
          {
            key: 'X-Robots-Tag',
            value: 'noindex, nofollow, noarchive, nosnippet',
          },
        ],
      });
    });
    
    return headers;
  },
};

module.exports = nextConfig;
```

### Method 2: Vercel Configuration

```json
// vercel.json
{
  "headers": [
    {
      "source": "/(.*)",
      "has": [
        {
          "type": "host",
          "value": "your-project-*.vercel.app"
        }
      ],
      "headers": [
        {
          "key": "X-Robots-Tag",
          "value": "noindex, nofollow, noarchive, nosnippet"
        }
      ]
    },
    {
      "source": "/(.*)",
      "has": [
        {
          "type": "host", 
          "value": "staging.yourdomain.com"
        }
      ],
      "headers": [
        {
          "key": "X-Robots-Tag",
          "value": "noindex, nofollow"
        }
      ]
    }
  ],
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ]
}
```

## Dynamic Sitemap Generation

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
      url: `${baseUrl}/services`,
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.8,
    },
    {
      url: `${baseUrl}/contact`,
      lastModified: new Date(),
      changeFrequency: 'yearly',
      priority: 0.6,
    },
  ]
}
```

## Environment-Specific Analytics

```typescript
// utils/analytics.ts
export function shouldLoadAnalytics(): boolean {
  const hostname = typeof window !== 'undefined' ? window.location.hostname : '';
  const isProduction = process.env.NODE_ENV === 'production';
  const isMainDomain = hostname === 'www.yourdomain.com' || hostname === 'yourdomain.com';
  
  // Only load analytics on production main domain
  return isProduction && isMainDomain;
}

// Usage in layout or component
if (shouldLoadAnalytics()) {
  // Load Google Analytics/Tag Manager
}
```