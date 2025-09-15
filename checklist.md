# SEO Landing Page Checklist

A comprehensive checklist for optimizing landing pages for search engines. Each item links to detailed guides in the `/guide` directory.

## 🌐 Domain & Indexing Strategy

*See detailed guide: [Domain Indexing Strategy](./guide/domain-indexing.md)*

- [ ] **Main domain** (yourdomain.com) - Full SEO optimization
- [ ] **Subdomains** (subdomain.yourdomain.com) - Block from search engines
- [ ] **Preview links** - Block from search engines
- [ ] **Emergency response plan** - Ready for accidental indexing

## ⚙️ Technical Configuration

### Next.js Configuration
*See detailed guide: [Next.js SEO Configuration](./guide/nextjs-config.md)*

- [ ] **Environment-specific controls** implemented
- [ ] **Production-only SEO** features enabled
- [ ] **Headers configuration** (next.config.js or Vercel)
- [ ] **Dynamic sitemap** generation
- [ ] **Analytics integration** (environment-specific)

### Robots.txt
*See detailed guide: [Robots.txt Configuration](./guide/robots-txt.md)*

- [ ] **Production robots.txt** - Allow all crawling
- [ ] **Subdomain/Development robots.txt** - Block all crawling
- [ ] **Sitemap reference** included in robots.txt
- [ ] **Testing completed** with robots.txt tester tools

## 📄 Meta Tags & HTML Structure

*See detailed guide: [Meta Tags & HTML Optimization](./guide/meta-tags.md)*

### Essential Meta Tags
- [ ] **DOCTYPE html** declaration
- [ ] **Lang attribute** on html element
- [ ] **Charset UTF-8** specified
- [ ] **Viewport meta tag** for mobile
- [ ] **Robots meta tag** configured correctly

### Title Optimization
- [ ] **Unique titles** for each page (50-60 characters)
- [ ] **Primary keyword** in title
- [ ] **Brand name** included
- [ ] **Title hierarchy** follows importance

### Meta Descriptions
- [ ] **Unique descriptions** for each page (150-160 characters)
- [ ] **Compelling copy** that encourages clicks
- [ ] **Primary keyword** included naturally
- [ ] **Call-to-action** when appropriate

### Header Structure
- [ ] **Single H1** per page with primary keyword
- [ ] **Logical H2-H6** hierarchy
- [ ] **Keywords in headers** (natural placement)
- [ ] **Descriptive header text** (not generic)

### Images & Media
- [ ] **Alt text** for all images
- [ ] **Descriptive filenames** for images
- [ ] **Optimized file sizes** (<100KB per image)
- [ ] **Modern formats** (WebP, AVIF)
- [ ] **Responsive images** with srcset

### Additional Elements
- [ ] **Favicon** configured (multiple sizes)
- [ ] **Canonical URLs** set correctly
- [ ] **Language/locale** meta tags
- [ ] **Open Graph** tags for social sharing
- [ ] **Twitter Card** tags

## 🏗️ Structured Data

*See detailed guide: [Structured Data Implementation](./guide/structured-data.md)*

### Schema Markup Types
- [ ] **Organization schema** (company info)
- [ ] **Local Business schema** (if applicable)
- [ ] **Website schema** (site navigation)
- [ ] **Breadcrumb schema** (navigation path)
- [ ] **Article schema** (blog posts/content)
- [ ] **FAQ schema** (frequently asked questions)
- [ ] **Service schema** (services offered)
- [ ] **Review schema** (customer reviews)

### Implementation
- [ ] **JSON-LD format** used (recommended)
- [ ] **Schema validation** completed
- [ ] **Google Rich Results** testing passed
- [ ] **Structured data** visible in search results

## ⚡ Performance Optimization

*See detailed guide: [Performance Optimization](./guide/performance.md)*

### Core Web Vitals
- [ ] **Largest Contentful Paint (LCP)** ≤ 2.5s
- [ ] **First Input Delay (FID)** ≤ 100ms
- [ ] **Cumulative Layout Shift (CLS)** ≤ 0.1
- [ ] **PageSpeed Insights** score 90+

### Performance Requirements
- [ ] **Page load speed** < 3 seconds
- [ ] **Images optimized** (WebP/AVIF, proper sizing)
- [ ] **Lazy loading** for below-the-fold images
- [ ] **CSS/JS minified** and compressed
- [ ] **HTTPS enabled** with valid SSL

### Optimization Techniques
- [ ] **Critical CSS** inlined
- [ ] **Non-critical CSS** loaded asynchronously
- [ ] **JavaScript** code splitting implemented
- [ ] **Font optimization** (font-display: swap)
- [ ] **Caching strategies** configured
- [ ] **CDN** setup for static assets

## 📊 Analytics & Tracking

*See detailed guide: [Analytics & Tracking Setup](./guide/analytics.md)*

### Google Analytics 4
- [ ] **GA4 property** created and configured
- [ ] **Environment-specific** tracking (production only)
- [ ] **Custom events** implemented
- [ ] **Conversion goals** defined
- [ ] **E-commerce tracking** (if applicable)

### Google Search Console
- [ ] **Property verified** (HTML meta tag or DNS)
- [ ] **Sitemap submitted** and indexed
- [ ] **URL inspection** completed for key pages
- [ ] **Coverage report** reviewed
- [ ] **Performance monitoring** active

### Additional Tracking
- [ ] **Core Web Vitals** monitoring
- [ ] **Error tracking** implemented
- [ ] **User behavior** analysis setup
- [ ] **Conversion funnel** tracking
- [ ] **Privacy compliance** (GDPR/CCPA)

## 🔍 Content Optimization

### Keyword Strategy
- [ ] **Primary keyword** research completed
- [ ] **Long-tail keywords** identified
- [ ] **Keyword density** optimized (1-2%)
- [ ] **Semantic keywords** included
- [ ] **Competitor analysis** completed

### Content Quality
- [ ] **Original, valuable content** created
- [ ] **Content length** appropriate (300+ words)
- [ ] **Reading level** appropriate for audience
- [ ] **Internal linking** strategy implemented
- [ ] **External links** to authoritative sources

### User Experience
- [ ] **Clear value proposition** above the fold
- [ ] **Compelling headlines** and subheadings
- [ ] **Scannable content** with bullet points
- [ ] **Call-to-action** buttons prominent
- [ ] **Mobile-friendly** design and content

## 🔗 Technical SEO

### URL Structure
- [ ] **Clean, descriptive URLs** (no parameters)
- [ ] **Hyphens** used for word separation
- [ ] **Lowercase URLs** consistently
- [ ] **Canonical URLs** prevent duplicate content
- [ ] **301 redirects** for changed URLs

### Site Architecture
- [ ] **XML sitemap** generated and submitted
- [ ] **Breadcrumb navigation** implemented
- [ ] **Internal linking** structure optimized
- [ ] **Site depth** minimized (3 clicks max)
- [ ] **404 error pages** customized and helpful

### Security & Accessibility
- [ ] **HTTPS** implemented site-wide
- [ ] **Security headers** configured
- [ ] **WCAG accessibility** standards met
- [ ] **Keyboard navigation** functional
- [ ] **Screen reader** compatibility

## 📱 Mobile Optimization

### Responsive Design
- [ ] **Mobile-first** design approach
- [ ] **Viewport meta tag** configured
- [ ] **Touch-friendly** buttons and links
- [ ] **Readable text** without zooming
- [ ] **Fast mobile** loading times

### Mobile-Specific Features
- [ ] **AMP pages** (if applicable)
- [ ] **Progressive Web App** features
- [ ] **Mobile usability** tested
- [ ] **Google Mobile-Friendly** test passed
- [ ] **Core Web Vitals** optimized for mobile

## 🌍 International SEO (if applicable)

### Multi-language Support
- [ ] **Hreflang tags** implemented
- [ ] **Language-specific** URLs
- [ ] **Localized content** created
- [ ] **Cultural adaptation** completed
- [ ] **Local search** optimization

### Geographic Targeting
- [ ] **Google Search Console** geo-targeting set
- [ ] **Local business** schema markup
- [ ] **NAP consistency** (Name, Address, Phone)
- [ ] **Local citations** and directories
- [ ] **Google My Business** optimized

## ✅ Pre-Launch Checklist

### Final Verification
- [ ] **All meta tags** properly implemented
- [ ] **Structured data** validated
- [ ] **Performance scores** meet targets
- [ ] **Mobile optimization** completed
- [ ] **Analytics tracking** functional
- [ ] **Search Console** setup complete
- [ ] **Sitemap** submitted and indexed
- [ ] **Robots.txt** configured correctly
- [ ] **SSL certificate** active
- [ ] **404 pages** customized

### Testing & Quality Assurance
- [ ] **Cross-browser** testing completed
- [ ] **Device testing** (mobile, tablet, desktop)
- [ ] **Page speed** testing passed
- [ ] **SEO audit** tools run (Screaming Frog, etc.)
- [ ] **Accessibility** testing completed
- [ ] **Link checking** (internal and external)
- [ ] **Form functionality** tested
- [ ] **Contact information** verified

## 📈 Post-Launch Monitoring

### Ongoing Tasks
- [ ] **Search Console** monitoring (weekly)
- [ ] **Analytics review** (weekly)
- [ ] **Performance monitoring** (daily)
- [ ] **Ranking tracking** (monthly)
- [ ] **Content updates** (regular)
- [ ] **Technical SEO** audits (quarterly)
- [ ] **Competitor analysis** (monthly)
- [ ] **User feedback** collection and analysis

---

## Quick Reference Links

- [Domain Indexing Strategy](./guide/domain-indexing.md)
- [Next.js SEO Configuration](./guide/nextjs-config.md)
- [Robots.txt Configuration](./guide/robots-txt.md)
- [Meta Tags & HTML Optimization](./guide/meta-tags.md)
- [Structured Data Implementation](./guide/structured-data.md)
- [Performance Optimization](./guide/performance.md)
- [Analytics & Tracking Setup](./guide/analytics.md)

**Note**: This checklist should be used in conjunction with the detailed guides in the `/guide` directory for complete implementation instructions and code examples.