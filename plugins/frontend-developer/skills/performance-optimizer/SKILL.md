---
name: performance-optimizer
description: Optimize frontend performance with bundle size reduction, lazy loading, and Core Web Vitals improvements. Use when improving page speed, reducing bundle size, or optimizing Core Web Vitals.
---

# Performance Optimizer

Optimize frontend performance for faster load times and better user experience.

## Quick Start

Measure with Lighthouse, optimize images, code split, lazy load, minimize bundle size, implement caching.

## Instructions

### Core Web Vitals

**Largest Contentful Paint (LCP):**
- Target: < 2.5s
- Measures: Loading performance
- Optimize: Images, fonts, server response

**First Input Delay (FID):**
- Target: < 100ms
- Measures: Interactivity
- Optimize: JavaScript execution, code splitting

**Cumulative Layout Shift (CLS):**
- Target: < 0.1
- Measures: Visual stability
- Optimize: Image dimensions, font loading

### Bundle Size Optimization

**Analyze bundle:**
```bash
# With webpack-bundle-analyzer
npm install --save-dev webpack-bundle-analyzer

# Add to webpack config
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

plugins: [
  new BundleAnalyzerPlugin()
]

# Or with Vite
npm install --save-dev rollup-plugin-visualizer
```

**Code splitting:**
```jsx
// Route-based splitting
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Profile = lazy(() => import('./pages/Profile'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/profile" element={<Profile />} />
      </Routes>
    </Suspense>
  );
}
```

**Component-based splitting:**
```jsx
const HeavyChart = lazy(() => import('./HeavyChart'));

function Dashboard() {
  return (
    <div>
      <Suspense fallback={<ChartSkeleton />}>
        <HeavyChart data={data} />
      </Suspense>
    </div>
  );
}
```

**Tree shaking:**
```js
// Bad: Imports entire library
import _ from 'lodash';

// Good: Import only what you need
import debounce from 'lodash/debounce';

// Or use lodash-es
import { debounce } from 'lodash-es';
```

### Image Optimization

**Use Next.js Image:**
```jsx
import Image from 'next/image';

<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1200}
  height={600}
  priority // For above-fold images
  placeholder="blur"
/>
```

**Lazy load images:**
```jsx
<img
  src="image.jpg"
  alt="Description"
  loading="lazy"
  width="800"
  height="600"
/>
```

**Use modern formats:**
```jsx
<picture>
  <source srcSet="image.avif" type="image/avif" />
  <source srcSet="image.webp" type="image/webp" />
  <img src="image.jpg" alt="Description" />
</picture>
```

**Responsive images:**
```jsx
<img
  srcSet="
    image-320w.jpg 320w,
    image-640w.jpg 640w,
    image-1280w.jpg 1280w
  "
  sizes="(max-width: 640px) 100vw, 640px"
  src="image-640w.jpg"
  alt="Description"
/>
```

### JavaScript Optimization

**Minimize JavaScript:**
```bash
# Production build
npm run build

# Check bundle size
ls -lh dist/assets/*.js
```

**Remove unused code:**
```js
// Use ES modules for tree shaking
export { specificFunction };

// Avoid default exports of large objects
```

**Defer non-critical JS:**
```html
<script src="analytics.js" defer></script>
<script src="non-critical.js" async></script>
```

### CSS Optimization

**Critical CSS:**
```jsx
// Inline critical CSS
<style dangerouslySetInnerHTML={{
  __html: criticalCSS
}} />

// Load rest async
<link
  rel="preload"
  href="/styles.css"
  as="style"
  onLoad="this.onload=null;this.rel='stylesheet'"
/>
```

**Remove unused CSS:**
```bash
# Use PurgeCSS
npm install --save-dev @fullhuman/postcss-purgecss

# Or use Tailwind's built-in purge
```

**CSS-in-JS optimization:**
```jsx
// Use styled-components with babel plugin
// Or use zero-runtime solutions like Linaria
```

### Lazy Loading

**Intersection Observer:**
```jsx
function LazyComponent() {
  const [isVisible, setIsVisible] = useState(false);
  const ref = useRef();
  
  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsVisible(true);
          observer.disconnect();
        }
      },
      { threshold: 0.1 }
    );
    
    if (ref.current) {
      observer.observe(ref.current);
    }
    
    return () => observer.disconnect();
  }, []);
  
  return (
    <div ref={ref}>
      {isVisible ? <HeavyComponent /> : <Placeholder />}
    </div>
  );
}
```

**React.lazy with retry:**
```jsx
function lazyWithRetry(componentImport) {
  return lazy(() =>
    componentImport().catch(() => {
      // Retry once
      return componentImport();
    })
  );
}

const Dashboard = lazyWithRetry(() => import('./Dashboard'));
```

### Caching Strategies

**Service Worker:**
```js
// Cache static assets
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open('v1').then((cache) => {
      return cache.addAll([
        '/',
        '/styles.css',
        '/script.js',
      ]);
    })
  );
});
```

**HTTP caching headers:**
```
# Static assets (immutable)
Cache-Control: public, max-age=31536000, immutable

# HTML (revalidate)
Cache-Control: no-cache

# API responses
Cache-Control: private, max-age=300
```

**React Query caching:**
```jsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000, // 5 minutes
      cacheTime: 10 * 60 * 1000, // 10 minutes
    },
  },
});
```

### Font Optimization

**Font loading:**
```css
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/myfont.woff2') format('woff2');
  font-display: swap; /* Show fallback immediately */
}
```

**Preload fonts:**
```html
<link
  rel="preload"
  href="/fonts/myfont.woff2"
  as="font"
  type="font/woff2"
  crossorigin
/>
```

**Variable fonts:**
```css
/* Single file for multiple weights */
@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter-var.woff2') format('woff2');
  font-weight: 100 900;
}
```

### Rendering Optimization

**Avoid layout thrashing:**
```js
// Bad: Read-write-read-write
element.style.width = element.offsetWidth + 10 + 'px';
element2.style.width = element2.offsetWidth + 10 + 'px';

// Good: Batch reads, then writes
const width1 = element.offsetWidth;
const width2 = element2.offsetWidth;
element.style.width = width1 + 10 + 'px';
element2.style.width = width2 + 10 + 'px';
```

**Use CSS transforms:**
```css
/* Bad: Triggers layout */
.element {
  left: 100px;
}

/* Good: GPU accelerated */
.element {
  transform: translateX(100px);
}
```

**Virtualize long lists:**
```jsx
import { useVirtualizer } from '@tanstack/react-virtual';

function VirtualList({ items }) {
  const parentRef = useRef();
  
  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 50,
  });
  
  return (
    <div ref={parentRef} style={{ height: '400px', overflow: 'auto' }}>
      <div style={{ height: `${virtualizer.getTotalSize()}px` }}>
        {virtualizer.getVirtualItems().map(virtualItem => (
          <div
            key={virtualItem.key}
            style={{
              position: 'absolute',
              top: 0,
              left: 0,
              width: '100%',
              transform: `translateY(${virtualItem.start}px)`,
            }}
          >
            {items[virtualItem.index].name}
          </div>
        ))}
      </div>
    </div>
  );
}
```

### Performance Monitoring

**Web Vitals:**
```jsx
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';

function sendToAnalytics(metric) {
  // Send to analytics service
  console.log(metric);
}

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getFCP(sendToAnalytics);
getLCP(sendToAnalytics);
getTTFB(sendToAnalytics);
```

**Performance Observer:**
```js
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    console.log(entry.name, entry.duration);
  }
});

observer.observe({ entryTypes: ['measure', 'navigation'] });
```

## Performance Checklist

**Loading:**
- [ ] Bundle size < 200KB (gzipped)
- [ ] Images optimized and lazy loaded
- [ ] Code split by route
- [ ] Critical CSS inlined
- [ ] Fonts preloaded

**Rendering:**
- [ ] No layout shifts (CLS < 0.1)
- [ ] Fast initial render (LCP < 2.5s)
- [ ] Smooth interactions (FID < 100ms)
- [ ] Virtual scrolling for long lists
- [ ] Memoization for expensive components

**Caching:**
- [ ] Service worker for offline
- [ ] HTTP caching headers
- [ ] API response caching
- [ ] Static assets cached

**Monitoring:**
- [ ] Lighthouse CI
- [ ] Real User Monitoring (RUM)
- [ ] Performance budgets
- [ ] Core Web Vitals tracking

## Best Practices

**Performance budgets:**
```json
{
  "budgets": [{
    "resourceSizes": [{
      "resourceType": "script",
      "budget": 200
    }, {
      "resourceType": "image",
      "budget": 500
    }]
  }]
}
```

**Lighthouse CI:**
```yaml
# .lighthouserc.json
{
  "ci": {
    "assert": {
      "assertions": {
        "categories:performance": ["error", {"minScore": 0.9}],
        "first-contentful-paint": ["error", {"maxNumericValue": 2000}]
      }
    }
  }
}
```

**Regular audits:**
- Weekly: Check bundle size
- Monthly: Full Lighthouse audit
- Quarterly: Performance review
