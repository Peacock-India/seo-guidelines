# Next.js SEO Configuration

Complete guide for configuring SEO metadata, robots.txt, and sitemaps in Next.js applications using the app directory to optimize search engine visibility and control indexing across different environments. **Recommended Open Graph image size: 1200 x 630 pixels (aspect ratio 1.91:1), under 1MB to 3MB(MAX) file size, using JPEG or PNG format.**

## SEO Configuration by Environment

| Environment | Index | Follow | Analytics | Sitemap | Canonical |
|-------------|-------|--------|-----------|---------|-----------|
| **Production Main Domain** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| **Staging/Preview** | ❌ No | ❌ No | ❌ No | ❌ No | ❌ No |
| **Development** | ❌ No | ❌ No | ❌ No | ❌ No | ❌ No |
| **Vercel/Netlify Preview** | ❌ No | ❌ No | ❌ No | ❌ No | ❌ No |

## Basic

Essential setup and core functionality that everyone needs.

### Environment Detection Utility

```typescript
// utils/seo-config.ts
export function getSEOConfig() {
  const hostname = typeof window !== 'undefined' 
    ? window.location.hostname 
    : process.env.VERCEL_URL || process.env.NETLIFY_URL || '';
    
  const isProduction = process.env.NODE_ENV === 'production';
  const isMainDomain = hostname === 'www.yourdomain.com' || hostname === 'yourdomain.com';
  const isPreviewDeploy = hostname.includes('vercel.app') || 
                          hostname.includes('netlify.app');
  const isSubdomain = hostname.includes('staging.') || 
                      hostname.includes('dev.');
  
  // Only allow indexing for production main domain
  const shouldIndex = isProduction && isMainDomain && !isPreviewDeploy && !isSubdomain;
  
  return {
    index: shouldIndex,
    follow: shouldIndex,
    canonical: shouldIndex ? `https://www.yourdomain.com` : null,
    includeAnalytics: shouldIndex,
    includeSitemap: shouldIndex,
  };
}
```

### Basic Metadata Configuration

```typescript
// app/layout.tsx
import { Metadata } from 'next'
import { getSEOConfig } from '@/utils/seo-config'

const seoConfig = getSEOConfig()
const SITE_URL = process.env.NEXT_PUBLIC_SITE_URL || "https://www.yourdomain.com"
const SITE_NAME = "Your Brand Name"

export const metadata: Metadata = {
  metadataBase: new URL(SITE_URL),
  
  // SEO Control
  robots: {
    index: seoConfig.index,
    follow: seoConfig.follow,
    googleBot: {
      index: seoConfig.index,
      follow: seoConfig.follow,
      'max-video-preview': -1,
      'max-image-preview': 'large',
      'max-snippet': -1,
    },
  },
  
  // Basic Info (only for indexable domains)
  ...(seoConfig.index ? {
    title: {
      default: "Your Landing Page Title - Brand Name",
      template: "%s | Your Brand Name"
    },
    description: "Compelling meta description under 160 characters with target keywords",
    keywords: ["primary keyword", "secondary keyword", "brand name"],
    authors: [{ name: "Your Name" }],
    creator: "Your Name",
    publisher: "Your Brand Name",
  } : {
    title: "Development Site - Not for Public",
    description: "This is a development environment",
  }),
  
  // Canonical (only for indexable domains)
  ...(seoConfig.canonical && {
    alternates: {
      canonical: seoConfig.canonical,
    },
  }),
  
  // Open Graph (only for indexable domains)
  ...(seoConfig.index && {
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
  }),
  
  // Verification
  verification: {
    google: "your-google-verification-code",
  },
}
```

### Dynamic Robots.txt Generation

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'
import { getSEOConfig } from '@/utils/seo-config'

export default function robots(): MetadataRoute.Robots {
  const seoConfig = getSEOConfig()
  const baseUrl = 'https://www.yourdomain.com'
  
  if (!seoConfig.index) {
    // Block everything for non-production
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

### Basic Sitemap Generation

```typescript
// app/sitemap.ts
import { MetadataRoute } from 'next'
import { getSEOConfig } from '@/utils/seo-config'

export default function sitemap(): MetadataRoute.Sitemap {
  const seoConfig = getSEOConfig()
  const baseUrl = 'https://www.yourdomain.com'
  
  // Only generate sitemap for indexable domains
  if (!seoConfig.includeSitemap) {
    return []
  }

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

### Basic Checklist

- [ ] Create `getSEOConfig()` utility for environment detection
- [ ] Configure metadata in root `layout.tsx` with conditional content
- [ ] Set up dynamic `robots.ts` file with environment-based rules
- [ ] Create `sitemap.ts` with your site's main pages
- [ ] Add Google Search Console verification code
- [ ] Configure environment variables for site URL and name
- [ ] Test SEO configuration on different environments
- [ ] Verify robots.txt and sitemap.xml accessibility

## Advanced

Optional features and advanced configurations.

### Dynamic Metadata Generation

```typescript
// app/blog/[slug]/page.tsx
import { Metadata } from 'next'

export async function generateMetadata({ params }): Promise<Metadata> {
  // Fetch data based on the slug
  const post = await fetch(`/api/posts/${params.slug}`).then(res => res.json())
  
  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.featuredImage],
    },
    twitter: {
      card: 'summary_large_image',
      title: post.title,
      description: post.excerpt,
      images: [post.featuredImage],
    },
  }
}
```

### Multi-Language SEO Configuration

```typescript
// app/layout.tsx
export const metadata: Metadata = {
  // ... other metadata
  alternates: {
    canonical: 'https://www.yourdomain.com',
    languages: {
      'en-US': 'https://www.yourdomain.com/en',
      'es-ES': 'https://www.yourdomain.com/es',
      'fr-FR': 'https://www.yourdomain.com/fr',
    },
  },
}
```

### Advanced Checklist

- [ ] Implement dynamic metadata generation for blog/product pages
- [ ] Add multi-language alternate links if applicable
- [ ] Set up structured data (JSON-LD) for rich snippets
- [ ] Configure advanced Open Graph properties
- [ ] Test with Google's Rich Results Test tool
- [ ] Monitor SEO performance with Google Search Console

## References

- [Next.js Metadata API](https://nextjs.org/docs/app/api-reference/functions/generate-metadata)
- [Next.js Adding Metadata](https://nextjs.org/learn/dashboard-app/adding-metadata)
- [Next.js robots.txt Configuration](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/robots)
- [Next.js Sitemap Generation](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/sitemap)
- [Next.js Custom Headers](https://nextjs.org/docs/pages/api-reference/config/next-config-js/headers)
- [Google Search Console](https://search.google.com/search-console)