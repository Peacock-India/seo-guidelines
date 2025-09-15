Here's the simplified `meta-tags.md`:

```markdown
# Meta Tags & HTML Optimization

## Essential Meta Tags

### Basic HTML Setup

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <meta name="format-detection" content="telephone=no" />
  
  <!-- Google Verification (if needed) -->
  <meta name="google-site-verification" content="YOUR_VERIFICATION_CODE" />
</head>
```

### Robots Meta Tags

#### Production Domain
```html
<meta name="robots" content="index, follow, max-image-preview:large, max-snippet:-1, max-video-preview:-1">
```

#### Blocked Domains
```html
<meta name="robots" content="noindex, nofollow, noarchive, nosnippet, noimageindex">
<meta name="googlebot" content="noindex, nofollow">
```

## Title & Description

### Title Tag (50-60 characters)

```html
<title>Primary Keyword | Secondary Keyword | Brand Name</title>
```

✅ **Good Examples:**
- `Web Design Services | Professional UI/UX | YourBrand`
- `SEO Checklist 2024 | Complete Guide | YourBrand`

❌ **Bad Examples:**
- `Welcome to Our Website`
- `Home Page`

### Meta Description (150-160 characters)

```html
<meta name="description" content="Clear value proposition with primary keyword. Include call-to-action. Keep under 160 characters for full display.">
```

✅ **Good Example:**
```html
<meta name="description" content="Professional web design services that convert visitors into customers. Get a free quote today and boost your online presence.">
```

## Header Structure

### Proper Hierarchy

```html
<h1>One H1 per page - Primary keyword focus</h1>

<h2>Major Section Heading</h2>
  <h3>Subsection under H2</h3>
  <h3>Another subsection</h3>

<h2>Another Major Section</h2>
  <h3>Subsection</h3>
```

**Rules:**
- Only ONE H1 per page
- Don't skip levels (H1 → H3)
- Include keywords naturally

## Images

### Optimized Image

```html
<img 
  src="/image.webp" 
  alt="Descriptive text with keyword when relevant"
  width="800"
  height="600"
  loading="lazy"
/>
```

### Next.js Image

```jsx
import Image from 'next/image'

<Image
  src="/hero.jpg"
  alt="Descriptive alt text"
  width={800}
  height={600}
  priority // For above-fold images
  loading="lazy" // For below-fold images
/>
```

**Rules:**
- Alt text on ALL images
- Keep images under 100KB - 600KB(MAX)
- Use WebP/AVIF formats. WebP is recommended. 
- Specify width/height

## Canonical & Language

### Canonical URL

```html
<link rel="canonical" href="https://www.yourdomain.com/current-page/" />
```

### Language

```html
<html lang="en">
<!-- or -->
<html lang="en-US">
```

### International (if needed)

```html
<link rel="alternate" hreflang="en" href="https://example.com/" />
<link rel="alternate" hreflang="es" href="https://example.com/es/" />
<link rel="alternate" hreflang="x-default" href="https://example.com/" />
```

## Favicon & Icons

```html
<link rel="icon" href="/favicon.ico" sizes="32x32" />
<link rel="icon" href="/favicon.svg" type="image/svg+xml" />
<link rel="apple-touch-icon" href="/apple-touch-icon.png" />
<link rel="manifest" href="/site.webmanifest" />
<meta name="theme-color" content="#000000" />
```

**Required Files:**
- `favicon.ico` (32x32px)
- `favicon.svg` (scalable)
- `apple-touch-icon.png` (180x180px)
- `site.webmanifest`

## Open Graph & Social

### Open Graph (Facebook)

```html
<meta property="og:type" content="website" />
<meta property="og:title" content="Page Title - Keep Under 60 Characters" />
<meta property="og:description" content="Description - Keep under 160 characters" />
<meta property="og:image" content="https://yourdomain.com/og-image.jpg" />
<meta property="og:url" content="https://yourdomain.com" />
<meta property="og:site_name" content="Your Site Name" />
```

### Twitter Card

```html
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="Title - Keep Under 70 Characters" />
<meta name="twitter:description" content="Description - Keep under 200 characters" />
<meta name="twitter:image" content="https://yourdomain.com/twitter-image.jpg" />
<meta name="twitter:site" content="@yourhandle" />
```

**Image Requirements:**
- OG Image: 1200x630px
- Twitter Image: 1200x600px

---

## Complete Example

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  
  <!-- SEO Meta Tags -->
  <title>Web Design Services | Professional UI/UX | YourBrand</title>
  <meta name="description" content="Professional web design services that convert visitors into customers. Get a free quote today and boost your online presence.">
  <meta name="robots" content="index, follow">
  <link rel="canonical" href="https://www.yourdomain.com/" />
  
  <!-- Favicon -->
  <link rel="icon" href="/favicon.ico" sizes="32x32" />
  <link rel="apple-touch-icon" href="/apple-touch-icon.png" />
  
  <!-- Open Graph -->
  <meta property="og:type" content="website" />
  <meta property="og:title" content="Professional Web Design Services | YourBrand" />
  <meta property="og:description" content="Transform your online presence with our expert web design services. Get a free quote today." />
  <meta property="og:image" content="https://yourdomain.com/og-image.jpg" />
  <meta property="og:url" content="https://yourdomain.com" />
  
  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content="Professional Web Design Services" />
  <meta name="twitter:description" content="Transform your online presence with our expert web design services." />
  <meta name="twitter:image" content="https://yourdomain.com/twitter-image.jpg" />
</head>
```
```