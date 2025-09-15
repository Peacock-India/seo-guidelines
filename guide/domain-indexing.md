# Domain Indexing Strategy

Proper domain indexing control is crucial for SEO success and preventing duplicate content issues.

## Main Domain (Production - Allow Indexing)

- `https://www.yourdomain.com` → **INDEX: YES**
- Full SEO optimization
- Complete metadata
- Sitemap submission

## Subdomains (Development/Staging - Block Indexing)

- `https://staging.yourdomain.com` → **INDEX: NO**
- `https://dev.yourdomain.com` → **INDEX: NO**
- Prevent SEO dilution

## Vercel/Netlify Preview Links (Block Indexing)

- `https://project-xyz123.vercel.app` → **INDEX: NO**
- `https://deploy-preview-123--site.netlify.app` → **INDEX: NO**
- Avoid duplicate content issues

## Implementation Strategy

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
// Usage in layout.tsx
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

## Emergency Response

### Wrong Domain Getting Indexed

#### Immediate Actions (Within 24 Hours)

1. Add noindex meta tags to unwanted domain immediately
2. Update robots.txt to "Disallow: /" for unwanted domain  
3. Submit URL removal request in Google Search Console
4. Set up 301 redirects from unwanted to correct domain
5. Check all internal links point to correct domain

#### Emergency Blocking Code

```html
<!-- Add to <head> of unwanted domain immediately -->
<meta name="robots" content="noindex, nofollow, noarchive, nosnippet, noimageindex">
<meta name="googlebot" content="noindex, nofollow">
```

```txt
# Update robots.txt immediately
User-agent: *
Disallow: /
```

#### Google Search Console Actions

1. Go to "Removals" → "New Request"
2. Select "Remove all URLs with this prefix"  
3. Enter: https://unwanted-domain.com
4. Submit request (provides 6 months temporary removal)
5. Monitor "URL Inspection" tool for removal confirmation