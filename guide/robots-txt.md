# Robots.txt Configuration

Proper robots.txt setup for controlling search engine crawling across different environments.

## Production Domain robots.txt

```txt
# /public/robots.txt - Main Domain (Allow Indexing)
User-agent: *
Allow: /

# Block admin and private areas
Disallow: /admin/
Disallow: /private/
Disallow: /api/
Disallow: /_next/
Disallow: /dashboard/

# Block common CMS paths
Disallow: /wp-admin/
Disallow: /wp-content/uploads/
Disallow: /.well-known/

# Allow important files
Allow: /sitemap.xml
Allow: /robots.txt
Allow: /favicon.ico

# Sitemap location
Sitemap: https://www.yourdomain.com/sitemap.xml

# Crawl delay (optional)
Crawl-delay: 1
```

## Subdomain/Development robots.txt

```txt
# /public/robots.txt - Subdomains/Development (Block All)
User-agent: *
Disallow: /

# No sitemap for blocked domains
```

## Best Practices

### What to Block

- **Admin areas**: `/admin/`, `/dashboard/`
- **API endpoints**: `/api/`
- **Private content**: `/private/`, `/internal/`
- **Build artifacts**: `/_next/`, `/build/`
- **Development files**: `/dev/`, `/test/`
- **User-specific content**: `/user/`, `/account/`

### What to Allow

- **Public pages**: All main content pages
- **Static assets**: CSS, JS, images (when needed for rendering)
- **Sitemap**: Always allow sitemap.xml
- **Favicon**: Allow favicon.ico

### Common Mistakes

❌ **Don't block CSS/JS if needed for rendering**
```txt
# This can hurt SEO if styles are needed
Disallow: /css/
Disallow: /js/
```

❌ **Don't use robots.txt for sensitive content**
```txt
# This actually advertises the existence of sensitive areas
Disallow: /secret-admin/
Disallow: /confidential/
```

✅ **Use proper authentication instead**

### Testing Your Robots.txt

1. **Google Search Console**: Use the robots.txt Tester
2. **Manual check**: Visit `https://yourdomain.com/robots.txt`
3. **Validate syntax**: Ensure proper formatting
4. **Test crawl paths**: Verify blocked/allowed paths work as expected

### Dynamic Robots.txt (Next.js)

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'
 
export default function robots(): MetadataRoute.Robots {
  const baseUrl = 'https://www.yourdomain.com'
  
  // Check if this is a production domain
  const isProduction = process.env.NODE_ENV === 'production'
  const hostname = process.env.VERCEL_URL || process.env.NETLIFY_URL || ''
  const isMainDomain = !hostname.includes('vercel.app') && !hostname.includes('netlify.app')
  
  if (isProduction && isMainDomain) {
    return {
      rules: {
        userAgent: '*',
        allow: '/',
        disallow: ['/admin/', '/api/', '/private/'],
      },
      sitemap: `${baseUrl}/sitemap.xml`,
    }
  }
  
  // Block everything for non-production domains
  return {
    rules: {
      userAgent: '*',
      disallow: '/',
    },
  }
}
```