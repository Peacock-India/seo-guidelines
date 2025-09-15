# Structured Data (Schema Markup)

Implement structured data to help search engines understand your content and enable rich snippets.

## Organization Schema

### Basic Organization Markup

```json
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Your Business Name",
  "description": "Brief description of your business",
  "url": "https://www.yourdomain.com",
  "logo": "https://www.yourdomain.com/logo.png",
  "contactPoint": {
    "@type": "ContactPoint",
    "telephone": "+1-234-567-8900",
    "contactType": "Customer Service",
    "email": "contact@yourdomain.com"
  },
  "sameAs": [
    "https://www.facebook.com/yourpage",
    "https://www.twitter.com/yourhandle",
    "https://www.linkedin.com/company/yourcompany"
  ]
}
```

### Implementation in HTML

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Your Business Name",
  "url": "https://www.yourdomain.com",
  "logo": "https://www.yourdomain.com/logo.png"
}
</script>
```

## Local Business Schema

### For Physical Locations

```json
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "Your Business Name",
  "description": "Your business description",
  "url": "https://www.yourdomain.com",
  "telephone": "+1-234-567-8900",
  "email": "contact@yourdomain.com",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "123 Main Street",
    "addressLocality": "Your City",
    "addressRegion": "Your State",
    "postalCode": "12345",
    "addressCountry": "US"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": "40.7128",
    "longitude": "-74.0060"
  },
  "openingHours": "Mo-Fr 09:00-17:00",
  "priceRange": "$$",
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "4.8",
    "reviewCount": "127"
  }
}
```

## Website Schema

### Site Navigation

```json
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "Your Website Name",
  "url": "https://www.yourdomain.com",
  "potentialAction": {
    "@type": "SearchAction",
    "target": {
      "@type": "EntryPoint",
      "urlTemplate": "https://www.yourdomain.com/search?q={search_term_string}"
    },
    "query-input": "required name=search_term_string"
  }
}
```

## Breadcrumb Schema

### Navigation Breadcrumbs

```json
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    {
      "@type": "ListItem",
      "position": 1,
      "name": "Home",
      "item": "https://www.yourdomain.com"
    },
    {
      "@type": "ListItem",
      "position": 2,
      "name": "Services",
      "item": "https://www.yourdomain.com/services"
    },
    {
      "@type": "ListItem",
      "position": 3,
      "name": "Web Design",
      "item": "https://www.yourdomain.com/services/web-design"
    }
  ]
}
```

## Article Schema

### Blog Posts & Articles

```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Your Article Title",
  "description": "Brief description of the article",
  "image": "https://www.yourdomain.com/article-image.jpg",
  "author": {
    "@type": "Person",
    "name": "Author Name",
    "url": "https://www.yourdomain.com/author/author-name"
  },
  "publisher": {
    "@type": "Organization",
    "name": "Your Brand Name",
    "logo": {
      "@type": "ImageObject",
      "url": "https://www.yourdomain.com/logo.png"
    }
  },
  "datePublished": "2024-01-15",
  "dateModified": "2024-01-20",
  "mainEntityOfPage": {
    "@type": "WebPage",
    "@id": "https://www.yourdomain.com/article-url"
  }
}
```

## FAQ Schema

### Frequently Asked Questions

```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What services do you offer?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "We offer web design, development, and SEO services to help businesses grow their online presence."
      }
    },
    {
      "@type": "Question",
      "name": "How long does a project take?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Most projects are completed within 4-6 weeks, depending on complexity and requirements."
      }
    }
  ]
}
```

## Service Schema

### Professional Services

```json
{
  "@context": "https://schema.org",
  "@type": "Service",
  "name": "Web Design Service",
  "description": "Professional web design services for businesses",
  "provider": {
    "@type": "Organization",
    "name": "Your Business Name",
    "url": "https://www.yourdomain.com"
  },
  "areaServed": {
    "@type": "Country",
    "name": "United States"
  },
  "hasOfferCatalog": {
    "@type": "OfferCatalog",
    "name": "Web Design Services",
    "itemListElement": [
      {
        "@type": "Offer",
        "itemOffered": {
          "@type": "Service",
          "name": "Custom Website Design"
        }
      }
    ]
  }
}
```

## Review Schema

### Customer Reviews

```json
{
  "@context": "https://schema.org",
  "@type": "Review",
  "itemReviewed": {
    "@type": "Organization",
    "name": "Your Business Name"
  },
  "reviewRating": {
    "@type": "Rating",
    "ratingValue": "5",
    "bestRating": "5"
  },
  "name": "Excellent service and results",
  "author": {
    "@type": "Person",
    "name": "John Smith"
  },
  "reviewBody": "Outstanding web design service. Professional team, great communication, and delivered exactly what we needed.",
  "datePublished": "2024-01-15"
}
```

## Implementation Best Practices

### 1. Validation

- Use [Google's Rich Results Test](https://search.google.com/test/rich-results)
- Validate with [Schema.org validator](https://validator.schema.org/)
- Test in Google Search Console

### 2. Common Mistakes

❌ **Don't:**
- Use schema for content not visible to users
- Mark up irrelevant or misleading information
- Use deprecated schema types
- Include schema for every possible element

✅ **Do:**
- Mark up the most important content
- Keep schema accurate and up-to-date
- Use the most specific schema type available
- Test thoroughly before deployment

### 3. Next.js Implementation

```typescript
// components/StructuredData.tsx
interface StructuredDataProps {
  data: object;
}

export function StructuredData({ data }: StructuredDataProps) {
  return (
    <script
      type="application/ld+json"
      dangerouslySetInnerHTML={{ __html: JSON.stringify(data) }}
    />
  );
}

// Usage in page
import { StructuredData } from '@/components/StructuredData';

const organizationSchema = {
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Your Business Name",
  // ... rest of schema
};

export default function HomePage() {
  return (
    <>
      <StructuredData data={organizationSchema} />
      {/* Page content */}
    </>
  );
}
```

### 4. Priority Schema Types

For most landing pages, implement in this order:

1. **Organization** - Essential for brand recognition
2. **WebSite** - Helps with site navigation
3. **LocalBusiness** - If you have physical locations
4. **FAQ** - Great for long-tail keyword targeting
5. **Service** - For service-based businesses
6. **Review** - For social proof and trust