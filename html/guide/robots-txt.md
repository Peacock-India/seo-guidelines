# Robots.txt Configuration

## Production robots.txt (Allow Indexing)

```txt
# /public/robots.txt - Main Domain
User-agent: *
Allow: /

# Block admin and private areas
Disallow: /admin/
Disallow: /private/
Disallow: /api/
Disallow: /_next/
Disallow: /dashboard/

# Sitemap location
Sitemap: https://www.yourdomain.com/sitemap.xml
```

## Blocked Domain robots.txt

```txt
# /public/robots.txt - Staging/Dev/Preview
User-agent: *
Disallow: /
```

## What to Block vs Allow

### ✅ Always Block
- `/admin/` - Admin areas
- `/api/` - API endpoints  
- `/private/` - Private content
- `/_next/` - Next.js build files
- `/dashboard/` - User dashboards
- `/temp/` - Temporary files

### ✅ Always Allow
- `/` - Homepage
- `/sitemap.xml` - Sitemap
- `/robots.txt` - This file
- Public content pages
- Images needed for rendering

### ⚠️ Common Mistakes

❌ **Don't block CSS/JS if needed for rendering:**
```txt
# Bad - can hurt SEO
Disallow: /css/
Disallow: /js/
```

❌ **Don't advertise sensitive paths:**
```txt
# Bad - tells hackers where to look
Disallow: /secret-admin/
Disallow: /confidential-data/
```

## Dynamic robots.txt (Next.js)

```typescript
// app/robots.ts
import { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  const baseUrl = 'https://www.yourdomain.com'
  
  // Check environment
  const isProduction = process.env.NODE_ENV === 'production'
  const hostname = process.env.VERCEL_URL || ''
  const isMainDomain = !hostname.includes('vercel.app') && 
                       !hostname.includes('netlify.app')
  
  if (isProduction && isMainDomain) {
    // Production robots.txt
    return {
      rules: {
        userAgent: '*',
        allow: '/',
        disallow: ['/admin/', '/api/', '/private/', '/_next/'],
      },
      sitemap: `${baseUrl}/sitemap.xml`,
    }
  }
  
  // Block everything for non-production
  return {
    rules: {
      userAgent: '*',
      disallow: '/',
    },
  }
}
```

## Testing Your robots.txt

### 1. Manual Check
```bash
# Visit in browser
https://yourdomain.com/robots.txt
```

### 2. Google Search Console
- Go to **Settings** → **robots.txt Tester**
- Enter your URL
- Test specific paths

### 3. Command Line
```bash
# Check if accessible
curl https://yourdomain.com/robots.txt

# Check specific user agent
curl -A "Googlebot" https://yourdomain.com/robots.txt
```

### 4. Online Tools
- Google's robots.txt Tester
- Bing Webmaster Tools
- Technical SEO validators

## Multiple Environments Setup

### Using Environment Variables

```typescript
// app/robots.ts with environment detection
export default function robots(): MetadataRoute.Robots {
  const domain = process.env.NEXT_PUBLIC_DOMAIN
  
  const configs = {
    'www.yourdomain.com': {
      rules: { userAgent: '*', allow: '/', disallow: ['/admin/'] },
      sitemap: 'https://www.yourdomain.com/sitemap.xml',
    },
    'staging.yourdomain.com': {
      rules: { userAgent: '*', disallow: '/' },
    },
    'dev.yourdomain.com': {
      rules: { userAgent: '*', disallow: '/' },
    },
  }
  
  return configs[domain] || {
    rules: { userAgent: '*', disallow: '/' },
  }
}
```

## Crawl Delay (Optional)

```txt
# For sites with limited server resources
User-agent: *
Crawl-delay: 1
Allow: /
```

**Note:** Google ignores crawl-delay. Use Search Console crawl rate settings instead.

## Special Bots

```txt
# Block bad bots
User-agent: AhrefsBot
Disallow: /

User-agent: SemrushBot
Disallow: /

# Allow good bots
User-agent: Googlebot
Allow: /

User-agent: bingbot
Allow: /
```

---

## Quick Reference

### Production Template
```txt
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/
Sitemap: https://www.yourdomain.com/sitemap.xml
```

### Blocked Template
```txt
User-agent: *
Disallow: /
```

### Testing Checklist
- [ ] File accessible at `/robots.txt`
- [ ] Correct rules for environment
- [ ] Sitemap referenced (production only)
- [ ] No typos or syntax errors
- [ ] Tested in Search Console