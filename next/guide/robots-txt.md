# Robots.txt Configuration for Next.js

Complete guide for creating and managing robots.txt files in Next.js applications using the app directory to control search engine crawling and indexing across different environments.

## What to Block vs Allow

| Path Type | Action | Example | Reason |
|-----------|--------|---------|---------|
| **Public Pages** | ✅ Allow | `/`, `/about`, `/products` | Main content for indexing |
| **Admin Areas** | ❌ Block | `/admin/`, `/dashboard/` | Private administrative content |
| **API Routes** | ❌ Block | `/api/` | Backend endpoints, not for indexing |
| **Build Files** | ❌ Block | `/_next/` | Next.js internal files |
| **Private Content** | ❌ Block | `/private/`, `/user/` | User-specific or sensitive content |
| **Staging Domains** | ❌ Block All | `staging.domain.com` | Prevent duplicate content issues |

## Basic

Essential setup and core functionality that everyone needs.

### Static robots.txt File

```typescript
// app/robots.txt (Static file in app directory)
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/
Disallow: /_next/
Sitemap: https://www.yourdomain.com/sitemap.xml
```

### Dynamic robots.ts Generation

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: ['/admin/', '/api/', '/_next/', '/private/'],
    },
    sitemap: 'https://www.yourdomain.com/sitemap.xml',
  }
}
```

### Environment-Based Configuration

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  const baseUrl = process.env.NEXT_PUBLIC_DOMAIN || 'https://www.yourdomain.com'
  const isProduction = process.env.NODE_ENV === 'production'
  
  // Block everything in non-production environments
  if (!isProduction) {
    return {
      rules: {
        userAgent: '*',
        disallow: '/',
      },
    }
  }

  // Production robots.txt
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: ['/admin/', '/api/', '/_next/', '/private/'],
    },
    sitemap: `${baseUrl}/sitemap.xml`,
  }
}
```

### Basic Checklist

- [ ] Create `app/robots.ts` file in your Next.js project
- [ ] Configure basic rules to allow public content and block private areas
- [ ] Add sitemap reference to help search engines discover your content
- [ ] Block non-production environments from indexing
- [ ] Test robots.txt accessibility at `yourdomain.com/robots.txt`
- [ ] Verify correct rules are applied in different environments

## Advanced

Optional features and advanced configurations.

### Multiple User Agent Rules

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  return {
    rules: [
      {
        userAgent: 'Googlebot',
        allow: ['/'],
        disallow: '/private/',
      },
      {
        userAgent: ['Applebot', 'Bingbot'],
        allow: ['/'],
        disallow: ['/admin/', '/api/'],
      },
      {
        userAgent: '*',
        disallow: '/',
      },
    ],
    sitemap: 'https://www.yourdomain.com/sitemap.xml',
  }
}
```

### Domain-Specific Configuration

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'
import { headers } from 'next/headers'

export default function robots(): MetadataRoute.Robots {
  const headersList = headers()
  const domain = headersList.get('host') || 'localhost'
  
  // Configuration for different domains
  const configs = {
    'www.yourdomain.com': {
      rules: {
        userAgent: '*',
        allow: '/',
        disallow: ['/admin/', '/api/'],
      },
      sitemap: 'https://www.yourdomain.com/sitemap.xml',
    },
    'staging.yourdomain.com': {
      rules: {
        userAgent: '*',
        disallow: '/',
      },
    },
    'preview.yourdomain.com': {
      rules: {
        userAgent: '*',
        disallow: '/',
      },
    },
  }
  
  return configs[domain] || {
    rules: {
      userAgent: '*',
      disallow: '/',
    },
  }
}
```

### Crawl Delay Configuration

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  return {
    rules: [
      {
        userAgent: 'Googlebot',
        allow: ['/'],
        disallow: '/private/',
        crawlDelay: 2,
      },
      {
        userAgent: 'SemrushBot',
        disallow: '/',
      },
      {
        userAgent: '*',
        allow: '/',
        disallow: ['/admin/', '/api/'],
        crawlDelay: 1,
      },
    ],
    sitemap: 'https://www.yourdomain.com/sitemap.xml',
    host: 'https://www.yourdomain.com',
  }
}
```

### Emergency Blocking Configuration

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  // Emergency block list for leaked or unwanted domains
  const emergencyBlockDomains = [
    'old-domain.com',
    'leaked-staging.com',
    'preview-123.vercel.app'
  ]
  
  const currentDomain = process.env.VERCEL_URL || process.env.NEXT_PUBLIC_DOMAIN || ''
  const shouldEmergencyBlock = emergencyBlockDomains.some(domain => 
    currentDomain.includes(domain)
  )
  
  if (shouldEmergencyBlock) {
    return {
      rules: {
        userAgent: '*',
        disallow: '/',
      },
    }
  }

  // Regular production configuration
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: ['/admin/', '/api/', '/_next/'],
    },
    sitemap: 'https://www.yourdomain.com/sitemap.xml',
  }
}
```

### Advanced Checklist

- [ ] Configure user agent specific rules for different search engines
- [ ] Set up domain-specific robots.txt based on environment
- [ ] Add crawl delay for resource-intensive sites
- [ ] Implement emergency blocking for leaked domains
- [ ] Test robots.txt with Google Search Console robots.txt tester
- [ ] Monitor for unauthorized domains getting indexed
- [ ] Set up alerts for robots.txt accessibility issues

## References

- [Next.js robots.txt Official Documentation](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/robots)
- [Next.js SEO Guide - robots.txt](https://nextjs.org/learn/seo/robots-txt)
- [Robots Exclusion Standard](https://developers.google.com/search/docs/crawling-indexing/robots/intro)
- [Google Search Console robots.txt Tester](https://support.google.com/webmasters/answer/6062598)
- [Next.js Metadata API](https://nextjs.org/docs/app/api-reference/functions/generate-metadata)
- [MetadataRoute.Robots Type Reference](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/robots#robots-object)
