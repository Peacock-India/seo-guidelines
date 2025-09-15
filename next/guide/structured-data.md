# Structured Data (Schema Markup) for Next.js

Complete guide for implementing structured data using JSON-LD format in Next.js applications with the app directory to help search engines understand your content and enable rich snippets in search results.

## Schema Types and Benefits

| Schema Type | SEO Benefit | Rich Snippet Potential | Priority |
|-------------|-------------|------------------------|----------|
| **Organization** | Brand recognition, contact info | Logo, social links in knowledge panel | High |
| **LocalBusiness** | Local search visibility | Address, hours, reviews in local pack | High |
| **FAQ** | Long-tail keyword targeting | Expandable Q&A in search results | Medium |
| **Article/BlogPosting** | Content categorization | Author, date, headline in news results | Medium |
| **WebSite** | Site search functionality | Sitelinks search box | Low |
| **BreadcrumbList** | Navigation clarity | Breadcrumb trails in results | Low |

## Basic

Essential setup and core functionality that everyone needs.

### Basic JSON-LD Implementation

```typescript
// app/page.tsx - Basic Organization Schema
export default function HomePage() {
  const organizationSchema = {
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

  return (
    <section>
      {/* Add JSON-LD to your page */}
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{
          __html: JSON.stringify(organizationSchema).replace(/</g, '\\u003c'),
        }}
      />
      <h1>Your Business Name</h1>
      {/* Rest of your content */}
    </section>
  )
}
```

### Reusable Structured Data Component

```typescript
// components/StructuredData.tsx
interface StructuredDataProps {
  data: object
}

export function StructuredData({ data }: StructuredDataProps) {
  return (
    <script
      type="application/ld+json"
      dangerouslySetInnerHTML={{
        __html: JSON.stringify(data).replace(/</g, '\\u003c'),
      }}
    />
  )
}
```

### Local Business Schema

```typescript
// app/contact/page.tsx
import { StructuredData } from '@/components/StructuredData'

export default function ContactPage() {
  const localBusinessSchema = {
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
    "priceRange": "$$"
  }

  return (
    <section>
      <StructuredData data={localBusinessSchema} />
      <h1>Contact Us</h1>
      {/* Contact page content */}
    </section>
  )
}
```

### FAQ Schema Implementation

```typescript
// app/faq/page.tsx
import { StructuredData } from '@/components/StructuredData'

export default function FAQPage() {
  const faqSchema = {
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
      },
      {
        "@type": "Question",
        "name": "Do you provide ongoing support?",
        "acceptedAnswer": {
          "@type": "Answer",
          "text": "Yes, we offer maintenance packages and ongoing support for all our web projects."
        }
      }
    ]
  }

  return (
    <section>
      <StructuredData data={faqSchema} />
      <h1>Frequently Asked Questions</h1>
      
      <div>
        <h2>What services do you offer?</h2>
        <p>We offer web design, development, and SEO services to help businesses grow their online presence.</p>
        
        <h2>How long does a project take?</h2>
        <p>Most projects are completed within 4-6 weeks, depending on complexity and requirements.</p>
        
        <h2>Do you provide ongoing support?</h2>
        <p>Yes, we offer maintenance packages and ongoing support for all our web projects.</p>
      </div>
    </section>
  )
}
```

### Basic Checklist

- [ ] Install `schema-dts` package for TypeScript support
- [ ] Create reusable `StructuredData` component
- [ ] Implement Organization schema on homepage
- [ ] Add LocalBusiness schema if you have physical locations
- [ ] Set up FAQ schema for common questions
- [ ] Sanitize JSON-LD data to prevent XSS attacks
- [ ] Test structured data with Google's Rich Results Test
- [ ] Validate schemas with Schema.org validator

## Advanced

Optional features and advanced configurations.

### Dynamic Article Schema with generateMetadata

```typescript
// app/blog/[slug]/page.tsx
import { StructuredData } from '@/components/StructuredData'
import { Metadata } from 'next'

interface BlogPost {
  title: string
  excerpt: string
  content: string
  author: string
  publishedAt: string
  modifiedAt: string
  featuredImage: string
  slug: string
}

async function getBlogPost(slug: string): Promise<BlogPost> {
  // Fetch blog post data
  const res = await fetch(`${process.env.API_URL}/posts/${slug}`)
  return res.json()
}

export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await getBlogPost(params.slug)
  
  return {
    title: post.title,
    description: post.excerpt,
  }
}

export default async function BlogPostPage({ params }) {
  const post = await getBlogPost(params.slug)
  
  const articleSchema = {
    "@context": "https://schema.org",
    "@type": "BlogPosting",
    "headline": post.title,
    "description": post.excerpt,
    "image": post.featuredImage,
    "author": {
      "@type": "Person",
      "name": post.author,
    },
    "publisher": {
      "@type": "Organization",
      "name": "Your Brand Name",
      "logo": {
        "@type": "ImageObject",
        "url": "https://www.yourdomain.com/logo.png"
      }
    },
    "datePublished": post.publishedAt,
    "dateModified": post.modifiedAt,
    "mainEntityOfPage": {
      "@type": "WebPage",
      "@id": `https://www.yourdomain.com/blog/${post.slug}`
    }
  }

  return (
    <article>
      <StructuredData data={articleSchema} />
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  )
}
```

### TypeScript-Enhanced Structured Data

```typescript
// lib/structured-data.ts
import { WithContext, Organization, LocalBusiness, FAQPage, BlogPosting } from 'schema-dts'

export function createOrganizationSchema(): WithContext<Organization> {
  return {
    "@context": "https://schema.org",
    "@type": "Organization",
    "name": "Your Business Name",
    "url": "https://www.yourdomain.com",
    "logo": "https://www.yourdomain.com/logo.png",
    "contactPoint": {
      "@type": "ContactPoint",
      "telephone": "+1-234-567-8900",
      "contactType": "customer service"
    }
  }
}

export function createBlogPostSchema(post: {
  title: string
  excerpt: string
  author: string
  publishedAt: string
  slug: string
  featuredImage: string
}): WithContext<BlogPosting> {
  return {
    "@context": "https://schema.org",
    "@type": "BlogPosting",
    "headline": post.title,
    "description": post.excerpt,
    "author": {
      "@type": "Person",
      "name": post.author,
    },
    "datePublished": post.publishedAt,
    "image": post.featuredImage,
    "mainEntityOfPage": `https://www.yourdomain.com/blog/${post.slug}`
  }
}
```

### Multiple Schema Types on Single Page

```typescript
// app/services/page.tsx
import { StructuredData } from '@/components/StructuredData'

export default function ServicesPage() {
  const organizationSchema = {
    "@context": "https://schema.org",
    "@type": "Organization",
    "name": "Your Business Name",
    "url": "https://www.yourdomain.com"
  }

  const serviceSchema = {
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
    }
  }

  const breadcrumbSchema = {
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
      }
    ]
  }

  return (
    <section>
      <StructuredData data={organizationSchema} />
      <StructuredData data={serviceSchema} />
      <StructuredData data={breadcrumbSchema} />
      
      <h1>Our Services</h1>
      {/* Services content */}
    </section>
  )
}
```

### Advanced Checklist

- [ ] Implement TypeScript interfaces with `schema-dts` package
- [ ] Create schema helper functions for consistent data
- [ ] Set up dynamic schema generation for blog posts/products
- [ ] Implement multiple schema types on complex pages
- [ ] Add breadcrumb schema for better navigation
- [ ] Create review/rating schemas for social proof
- [ ] Test all schemas with Google's Structured Data Testing Tool
- [ ] Monitor rich snippet performance in Google Search Console

## References

- [Next.js JSON-LD Official Guide](https://nextjs.org/docs/app/guides/json-ld)
- [Schema.org Vocabulary Documentation](https://schema.org)
- [Google's Structured Data Guidelines](https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data)
- [Google Rich Results Test Tool](https://search.google.com/test/rich-results)
- [Schema.org Markup Validator](https://validator.schema.org)
- [schema-dts TypeScript Package](https://www.npmjs.com/package/schema-dts)
