# Meta Tags & HTML Optimization for Next.js

Complete guide for implementing meta tags, Open Graph metadata, and HTML optimization in Next.js applications using the app directory to improve SEO, social sharing, and user experience.

## Meta Tag Optimization Guidelines

| Meta Tag Type | Character Limits | SEO Impact | Social Sharing |
|---------------|------------------|------------|----------------|
| **Title** | 50-60 characters | High - Primary ranking factor | Used by all platforms |
| **Description** | 150-160 characters | Medium - Affects click-through rate | Used by most platforms |
| **OG Image** | 1200 x 630px (under 1-2MB) | Low - Not a ranking factor | Critical for engagement |
| **Alt Text** | 125 characters max | High - Accessibility & image SEO | Screen readers |
| **Canonical URL** | 125 characters max | High - Avoid duplicate content | Search engines |

## Basic

Essential setup and core functionality that everyone needs.

### Essential Metadata Configuration

```typescript
// app/layout.tsx
import { Metadata } from 'next'

export const metadata: Metadata = {
  // Required meta tags
  metadataBase: new URL('https://www.yourdomain.com'),
  
  // Title optimization
  title: {
    default: "Primary Keyword | Secondary Keyword | Brand Name",
    template: "%s | Brand Name"
  },
  
  // Meta description (150-160 characters)
  description: "Clear value proposition with primary keyword. Include call-to-action. Keep under 160 characters for full display.",
  
  // Essential SEO tags
  keywords: ["primary keyword", "secondary keyword", "brand name"],
  authors: [{ name: "Your Name" }],
  creator: "Your Name",
  publisher: "Brand Name",
  
  // Robots directive
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
  
  // Canonical URL
  alternates: {
    canonical: 'https://www.yourdomain.com',
  },
  
  // Verification codes
  verification: {
    google: 'your-google-verification-code',
  },
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

### Open Graph & Twitter Configuration

```typescript
// app/layout.tsx
export const metadata: Metadata = {
  // ... other metadata
  
  // Open Graph tags
  openGraph: {
    type: 'website',
    locale: 'en_US',
    url: 'https://www.yourdomain.com',
    siteName: 'Your Brand Name',
    title: 'Social Media Title - Keep Under 60 Characters',
    description: 'Social media description - keep under 120 characters',
    images: [
      {
        url: '/og-image.jpg', // 1200x630px
        width: 1200,
        height: 630,
        alt: 'Descriptive alt text for your brand',
      },
    ],
  },
  
  // Twitter Card tags
  twitter: {
    card: 'summary_large_image',
    site: '@yourhandle',
    creator: '@yourhandle',
    title: 'Twitter Title - Keep Under 70 Characters',
    description: 'Twitter description - keep under 200 characters',
    images: ['/twitter-image.jpg'], // 1200x600px
  },
}
```

### Favicon & Icons Setup

```typescript
// Place these files in your app directory:
// app/favicon.ico (32x32px) - Required
// app/icon.png (any size) - Optional
// app/apple-icon.png (180x180px) - Optional

// Next.js automatically detects and adds appropriate tags:
// <link rel="icon" href="/favicon.ico" sizes="any" />
// <link rel="icon" href="/icon.png" type="image/png" />
// <link rel="apple-touch-icon" href="/apple-icon.png" />
```

### Image Optimization

```tsx
// components/OptimizedImage.tsx
import Image from 'next/image'

export function OptimizedImage() {
  return (
    <>
      {/* Above-the-fold image with priority */}
      <Image
        src="/hero.webp"
        alt="Descriptive text with keyword when relevant"
        width={1200}
        height={600}
        priority
        placeholder="blur"
        blurDataURL="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD..."
      />
      
      {/* Below-the-fold images with lazy loading */}
      <Image
        src="/feature.webp"
        alt="Feature description with relevant keywords"
        width={800}
        height={400}
        loading="lazy"
        sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
      />
    </>
  )
}
```

### Proper Heading Structure

```tsx
// app/page.tsx
export default function HomePage() {
  return (
    <main>
      {/* Only ONE H1 per page */}
      <h1>Primary Keyword - Main Page Topic</h1>
      
      {/* Proper hierarchy - don't skip levels */}
      <section>
        <h2>Major Section Heading</h2>
        <h3>Subsection under H2</h3>
        <h3>Another subsection</h3>
      </section>
      
      <section>
        <h2>Another Major Section</h2>
        <h3>Subsection with secondary keywords</h3>
      </section>
    </main>
  )
}
```

### Basic Checklist

- [ ] Configure essential metadata in root `layout.tsx`
- [ ] Set up title template for consistent branding
- [ ] Add meta description under 160 characters
- [ ] Configure Open Graph and Twitter Card metadata
- [ ] Place `favicon.ico` in app directory
- [ ] Use Next.js `<Image>` component for all images
- [ ] Implement proper heading hierarchy (one H1 per page)
- [ ] Add descriptive alt text to all images

## Advanced

Optional features and advanced configurations.

### Dynamic Metadata Generation

```typescript
// app/blog/[slug]/page.tsx
import { Metadata } from 'next'

interface BlogPost {
  title: string
  excerpt: string
  author: string
  publishedAt: string
  featuredImage: string
  slug: string
}

async function getBlogPost(slug: string): Promise<BlogPost> {
  const res = await fetch(`${process.env.API_URL}/posts/${slug}`)
  if (!res.ok) throw new Error('Failed to fetch post')
  return res.json()
}

export async function generateMetadata(
  { params }: { params: { slug: string } }
): Promise<Metadata> {
  const post = await getBlogPost(params.slug)
  
  return {
    title: post.title,
    description: post.excerpt,
    authors: [{ name: post.author }],
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [
        {
          url: post.featuredImage,
          width: 1200,
          height: 630,
          alt: post.title,
        },
      ],
      publishedTime: post.publishedAt,
      type: 'article',
    },
    twitter: {
      card: 'summary_large_image',
      title: post.title,
      description: post.excerpt,
      images: [post.featuredImage],
    },
  }
}

export default async function BlogPost({ 
  params 
}: { 
  params: { slug: string } 
}) {
  const post = await getBlogPost(params.slug)
  
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.excerpt}</p>
      {/* Article content */}
    </article>
  )
}
```

### Environment-Specific Metadata

```typescript
// utils/metadata.ts
export function getEnvironmentMetadata() {
  const isProduction = process.env.NODE_ENV === 'production'
  const baseUrl = process.env.NEXT_PUBLIC_SITE_URL || 'http://localhost:3000'
  
  if (!isProduction) {
    return {
      title: 'Development Site - Not for Public',
      description: 'This is a development environment',
      robots: {
        index: false,
        follow: false,
      },
    }
  }
  
  return {
    robots: {
      index: true,
      follow: true,
    },
    metadataBase: new URL(baseUrl),
  }
}
```

```typescript
// app/layout.tsx
import { getEnvironmentMetadata } from '@/utils/metadata'

const envMetadata = getEnvironmentMetadata()

export const metadata: Metadata = {
  ...envMetadata,
  // ... rest of your metadata
}
```

### Multi-Language Support

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

### Custom Viewport Configuration

```typescript
// app/layout.tsx
import { Viewport } from 'next'

export const viewport: Viewport = {
  width: 'device-width',
  initialScale: 1,
  maximumScale: 1,
  userScalable: false, // Only if necessary for UX
  themeColor: [
    { media: '(prefers-color-scheme: light)', color: '#ffffff' },
    { media: '(prefers-color-scheme: dark)', color: '#000000' },
  ],
}
```

### Dynamic Open Graph Images

```tsx
// app/blog/[slug]/opengraph-image.tsx
import { ImageResponse } from 'next/og'

export const size = {
  width: 1200,
  height: 630,
}

export const contentType = 'image/png'

export default async function OGImage({
  params,
}: {
  params: { slug: string }
}) {
  const post = await getBlogPost(params.slug)
  
  return new ImageResponse(
    (
      <div
        style={{
          fontSize: 64,
          background: 'linear-gradient(135deg, #667eea 0%, #764ba2 100%)',
          width: '100%',
          height: '100%',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          color: 'white',
          textAlign: 'center',
          padding: '40px',
        }}
      >
        {post.title}
      </div>
    ),
    {
      ...size,
    }
  )
}
```

### Advanced Checklist

- [ ] Implement dynamic metadata generation for blog/product pages
- [ ] Set up environment-specific metadata configurations
- [ ] Configure multi-language alternate links if applicable
- [ ] Create dynamic Open Graph images with `next/og`
- [ ] Set up custom viewport configurations for PWA support
- [ ] Test metadata with social media validators
- [ ] Monitor Open Graph image generation performance
- [ ] Validate structured data with Google's Rich Results Test

## References

- [Next.js Metadata API Documentation](https://nextjs.org/docs/app/api-reference/functions/generate-metadata)
- [Next.js Metadata and OG Images Guide](https://nextjs.org/docs/app/getting-started/metadata-and-og-images)
- [Next.js Favicon and Icons Setup](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons)
- [Next.js Open Graph Image Generation](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/opengraph-image)
- [Next.js Image Optimization](https://nextjs.org/docs/app/getting-started/images)
- [Open Graph Protocol Specification](https://ogp.me/)