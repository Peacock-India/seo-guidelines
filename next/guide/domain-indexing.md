# Domain Indexing Strategy

## Indexing Rules

| Domain Type | Index? | Example |
|------------|--------|---------|
| Main Domain | ✅ YES | `yourdomain.com`, `www.yourdomain.com` |
| Subdomains | ❌ NO | `staging.yourdomain.com`, `dev.yourdomain.com` |
| Preview Links | ❌ NO | `project-xyz.vercel.app`, `preview-123.netlify.app` |
| Test Domains | ❌ NO | `yourdomain.surge.sh`, `yourdomain.github.io` |

## Implementation

### Environment Detection

```typescript
// utils/seo-config.ts
export function getSEOConfig() {
  const hostname = typeof window !== 'undefined' 
    ? window.location.hostname 
    : process.env.VERCEL_URL || process.env.NETLIFY_URL || '';
    
  const isProduction = process.env.NODE_ENV === 'production';
  const isMainDomain = hostname === 'www.yourdomain.com' || hostname === 'yourdomain.com';
  const isPreviewDeploy = hostname.includes('vercel.app') || 
                          hostname.includes('netlify.app') ||
                          hostname.includes('surge.sh');
  const isSubdomain = hostname.includes('staging.') || 
                      hostname.includes('dev.') || 
                      hostname.includes('test.');
  
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

### Usage in Layout

```typescript
// app/layout.tsx
const seoConfig = getSEOConfig();

export const metadata: Metadata = {
  robots: {
    index: seoConfig.index,
    follow: seoConfig.follow,
    googleBot: {
      index: seoConfig.index,
      follow: seoConfig.follow,
    },
  },
  // ... rest of metadata only if shouldIndex is true
};
```

### Meta Tags by Environment

#### Production (Main Domain)
```html
<meta name="robots" content="index, follow">
```

#### Blocked Domains
```html
<meta name="robots" content="noindex, nofollow, noarchive, nosnippet, noimageindex">
<meta name="googlebot" content="noindex, nofollow">
```

### Headers Configuration

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

### Method 2: Vercel Config
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

### Method 3: Middleware

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

---

## Emergency Response

### Wrong Domain Getting Indexed

#### Immediate Actions (Within 24 Hours)

1. **Add noindex meta tags** to unwanted domain immediately
2. **Update robots.txt** to "Disallow: /" for unwanted domain  
3. **Submit URL removal request** in Google Search Console
4. **Set up 301 redirects** from unwanted to correct domain
5. **Check all internal links** point to correct domain

#### Step 1: Block Immediately

```html
<!-- Add to <head> of unwanted domain -->
<meta name="robots" content="noindex, nofollow, noarchive, nosnippet, noimageindex">
<meta name="googlebot" content="noindex, nofollow">
```

```txt
# Update robots.txt immediately
User-agent: *
Disallow: /
```

#### Step 2: Google Search Console

1. Go to **"Removals"** → **"New Request"**
2. Select **"Remove all URLs with this prefix"**  
3. Enter: `https://unwanted-domain.com`
4. Submit request (provides 6 months temporary removal)
5. Monitor **"URL Inspection"** tool for removal confirmation

#### Step 3: Redirect Setup

```javascript
// next.config.js
module.exports = {
  async redirects() {
    return [{
      source: '/:path*',
      has: [{
        type: 'host',
        value: 'wrong-domain.com',
      }],
      destination: 'https://correct-domain.com/:path*',
      permanent: true,
    }];
  },
};
```

#### Step 4: Verify Fix

- [ ] Check Google: `site:wrong-domain.com`
- [ ] Monitor Search Console for removal
- [ ] Test robots.txt: `wrong-domain.com/robots.txt`
- [ ] Verify redirects are working

---

## Quick Test Commands

```bash
# Check if domain is indexed
site:yourdomain.com

# Check robots meta tag
curl -I https://yourdomain.com | grep -i x-robots

# Test robots.txt
curl https://yourdomain.com/robots.txt
```
