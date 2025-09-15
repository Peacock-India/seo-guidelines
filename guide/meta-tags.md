# Meta Tags & HTML Optimization

Essential meta tags and HTML elements for proper SEO implementation.

## Essential Meta Tags

### Basic HTML Meta Tags

```html
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<meta name="format-detection" content="telephone=no" />
<meta name="google-site-verification" content="YOUR_VERIFICATION_CODE" />
```

### Robots Meta Tags

#### Production Domain
```html
<meta name="robots" content="index, follow, max-image-preview:large, max-snippet:-1, max-video-preview:-1">
<meta name="googlebot" content="index, follow, max-video-preview:-1, max-image-preview:large, max-snippet:-1">
```

#### Blocked Domains
```html
<meta name="robots" content="noindex, nofollow, noarchive, nosnippet, noimageindex">
<meta name="googlebot" content="noindex, nofollow">
<meta name="bingbot" content="noindex, nofollow">
```

## Title Tag Optimization

### Best Practices

```html
<!-- Title tag best practices -->
<title>Primary Keyword | Secondary Keyword | Brand Name</title>
```

**Rules:**
- Keep under 60 characters
- Include primary keyword near beginning
- Make it compelling and clickable
- Each page should have unique title
- Use pipes (|) or hyphens (-) as separators

### Examples

✅ **Good Examples:**
```html
<title>Web Design Services | Professional UI/UX | YourBrand</title>
<title>SEO Checklist 2024 | Complete Guide | YourBrand</title>
<title>Contact Us | Get Free Quote | YourBrand</title>
```

❌ **Bad Examples:**
```html
<title>Welcome to Our Website</title>
<title>Home Page</title>
<title>This is the best web design company in the world offering amazing services</title>
```

## Meta Description

### Best Practices

```html
<meta name="description" content="Compelling description with primary keyword that describes value proposition and includes call-to-action. Keep under 160 characters for full display in search results.">
```

**Rules:**
- 150-160 characters optimal
- Include primary keyword naturally  
- Write for humans, not just search engines
- Include a call-to-action
- Make it unique for each page
- Avoid duplicate descriptions

### Examples

✅ **Good Examples:**
```html
<meta name="description" content="Professional web design services that convert visitors into customers. Get a free quote today and boost your online presence with our expert team.">

<meta name="description" content="Complete SEO checklist for 2024. Follow our step-by-step guide to improve your search rankings and drive more organic traffic to your site.">
```

❌ **Bad Examples:**
```html
<meta name="description" content="Welcome to our website">
<meta name="description" content="We are the best company ever and we do everything you need and more with amazing results that will blow your mind and exceed all expectations">
```

## Header Structure (H1-H6)

### Proper Hierarchy

```html
<!-- Proper header hierarchy -->
<h1>Primary Keyword + Clear Value Proposition</h1>

<h2>Major Section: Benefits of Our Service</h2>
  <h3>Specific Benefit 1</h3>
  <h3>Specific Benefit 2</h3>
  
<h2>How It Works - Simple Process</h2>
  <h3>Step 1: Getting Started</h3>
  <h3>Step 2: Implementation</h3>
  <h3>Step 3: Results</h3>

<h2>Customer Success Stories</h2>
  <h3>Case Study 1</h3>
  <h3>Case Study 2</h3>
```

**Rules:**
- Only one H1 per page
- Use H2-H6 in logical hierarchy
- Include keywords naturally
- Make headers descriptive
- Don't skip heading levels (H1 → H3)

## Image Optimization

### Optimized Image Example

```html
<img 
  src="/hero-image.webp" 
  alt="Descriptive alt text that includes relevant keywords when appropriate"
  width="800"
  height="600"
  loading="lazy"
  decoding="async"
  fetchpriority="high" // For above-the-fold images
/>
```

**Rules:**
- All images need descriptive alt text
- Use modern formats (WebP, AVIF)
- Specify width/height to prevent CLS
- Use appropriate loading strategy
- Compress images (aim for <100KB per image)
- Use descriptive file names

### Alt Text Best Practices

✅ **Good Alt Text:**
```html
<img alt="Professional web designer working on laptop creating responsive website layout">
<img alt="SEO performance dashboard showing 150% traffic increase">
<img alt="Happy customer testimonial from John Smith, CEO of TechCorp">
```

❌ **Bad Alt Text:**
```html
<img alt="image1">
<img alt="photo">
<img alt="">
<img alt="click here">
```

## Favicon & Icons

### Essential Favicon Setup

```html
<!-- Essential favicon setup -->
<link rel="icon" href="/favicon.ico" sizes="32x32" />
<link rel="icon" href="/favicon.svg" type="image/svg+xml" />
<link rel="apple-touch-icon" href="/apple-touch-icon.png" />
<link rel="manifest" href="/site.webmanifest" />
<meta name="theme-color" content="#000000" />
```

### Required Files

- `favicon.ico` (32x32px, for legacy browsers)
- `favicon.svg` (scalable vector format)
- `apple-touch-icon.png` (180x180px, for iOS)
- `site.webmanifest` (PWA manifest)

## Canonical URLs

### Prevent Duplicate Content

```html
<!-- Prevent duplicate content issues -->
<link rel="canonical" href="https://www.yourdomain.com/current-page/" />

<!-- For paginated content -->
<link rel="canonical" href="https://www.yourdomain.com/articles/" />
<link rel="next" href="https://www.yourdomain.com/articles/page-2/" />
```

**Rules:**
- Always use absolute URLs
- Point to the preferred version of the page
- Use for pagination, sorting, filtering
- Ensure canonical URL is accessible
- Don't canonical to different content

## Language and Locale

### HTML Lang Attribute

```html
<html lang="en">
<!-- or -->
<html lang="en-US">
```

### Hreflang for International Sites

```html
<link rel="alternate" hreflang="en" href="https://example.com/" />
<link rel="alternate" hreflang="es" href="https://example.com/es/" />
<link rel="alternate" hreflang="fr" href="https://example.com/fr/" />
<link rel="alternate" hreflang="x-default" href="https://example.com/" />
```