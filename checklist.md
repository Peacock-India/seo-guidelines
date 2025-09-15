# SEO Landing Page Checklist

## ✅ Must-Have SEO Checklist

### Domain Control
*See: [Domain Indexing Strategy](./guide/next/domain-indexing.md)*

- [ ] Main domain (yourdomain.com) - Allow indexing
- [ ] Subdomains (staging/dev) - Block indexing  
- [ ] Preview links (vercel.app/netlify.app) - Block indexing

### Essential Meta Tags
*See: [Meta Tags Guide](./guide/next/meta-tags.md)*

- [ ] Unique title tag (50-60 characters)
- [ ] Meta description (150-160 characters)
- [ ] Robots meta tag configured
- [ ] Viewport meta tag
- [ ] Canonical URL set
- [ ] Favicon configured

### Content Basics
*See: [Meta Tags Guide](./guide/next/meta-tags.md)*

- [ ] One H1 tag per page
- [ ] Alt text for all images
- [ ] Images under 100KB
- [ ] Clean URLs (no parameters)
- [ ] Proper heading hierarchy (H1-H6)

### Technical Setup
*See: [Next.js Config](./guide/next/nextjs-config.md) | [Robots.txt](./guide/next/robots-txt.md)*

- [ ] robots.txt configured
- [ ] XML sitemap created
- [ ] HTTPS enabled
- [ ] Environment-specific controls
- [ ] Headers configured

### Performance
*See: [Performance Guide](./guide/next/performance.md)*

- [ ] Page loads under 3 seconds
- [ ] Core Web Vitals passing
- [ ] Mobile-friendly design
- [ ] Images optimized (WebP/AVIF)
- [ ] CSS/JS minified

### Analytics
*See: [Analytics Guide](./guide/next/analytics.md)*

- [ ] Google Analytics installed (production only)
- [ ] Google Search Console verified
- [ ] Sitemap submitted
- [ ] Conversion tracking setup

### Structured Data
*See: [Structured Data Guide](./guide/next/structured-data.md)*

- [ ] Organization schema
- [ ] Website schema
- [ ] FAQ schema (if applicable)
- [ ] Schema validation passed

---

## 🚨 Emergency Fixes

### Wrong Domain Gets Indexed
*See: [Domain Indexing Emergency](./next/guide/domain-indexing.md#emergency-response)*

**Do immediately (within 24 hour):**

1. [ ] Add noindex meta tag to unwanted domain
2. [ ] Update robots.txt to block all
3. [ ] Submit removal request in Search Console
4. [ ] Set up 301 redirect to correct domain
5. [ ] Add X-Robots-Tag header

**Follow-up:**
- [ ] Monitor removal confirmation
- [ ] Check all internal links
- [ ] Verify main domain indexing

---

## 📋 Pre-Launch Final Check

- [ ] All meta tags implemented
- [ ] Robots.txt tested
- [ ] Performance scores meet targets (90+)
- [ ] Analytics working (production only)
- [ ] Search Console setup complete
- [ ] Sitemap submitted
- [ ] SSL certificate active
- [ ] 404 page customized
- [ ] Mobile testing passed
- [ ] Schema markup validated