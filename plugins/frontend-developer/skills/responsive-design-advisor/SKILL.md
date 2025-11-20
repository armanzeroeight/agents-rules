---
name: responsive-design-advisor
description: Design responsive layouts with breakpoints, mobile-first approach, and flexible grids. Use when creating responsive designs, implementing breakpoints, or optimizing for multiple screen sizes.
---

# Responsive Design Advisor

Design responsive layouts that work across all device sizes.

## Quick Start

Use mobile-first approach, flexible units (rem, %), CSS Grid/Flexbox, and test on real devices.

## Instructions

### Mobile-First Approach

**Start with mobile, enhance for larger screens:**
```css
/* Base styles (mobile) */
.container {
  padding: 1rem;
}

/* Tablet */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    padding: 3rem;
    max-width: 1200px;
    margin: 0 auto;
  }
}
```

### Common Breakpoints

**Standard breakpoints:**
```css
/* Mobile: 0-639px (default) */

/* Tablet: 640px+ */
@media (min-width: 640px) { }

/* Laptop: 1024px+ */
@media (min-width: 1024px) { }

/* Desktop: 1280px+ */
@media (min-width: 1280px) { }

/* Large: 1536px+ */
@media (min-width: 1536px) { }
```

**Tailwind breakpoints:**
```jsx
<div className="
  w-full          // Mobile
  md:w-1/2        // Tablet
  lg:w-1/3        // Desktop
">
  Content
</div>
```

### Flexible Layouts

**CSS Grid:**
```css
.grid {
  display: grid;
  gap: 1rem;
  
  /* Mobile: 1 column */
  grid-template-columns: 1fr;
}

@media (min-width: 768px) {
  .grid {
    /* Tablet: 2 columns */
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (min-width: 1024px) {
  .grid {
    /* Desktop: 3 columns */
    grid-template-columns: repeat(3, 1fr);
  }
}
```

**Auto-fit grid:**
```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}
```

**Flexbox:**
```css
.flex-container {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.flex-item {
  flex: 1 1 100%; /* Mobile: full width */
}

@media (min-width: 768px) {
  .flex-item {
    flex: 1 1 calc(50% - 0.5rem); /* Tablet: 2 columns */
  }
}

@media (min-width: 1024px) {
  .flex-item {
    flex: 1 1 calc(33.333% - 0.667rem); /* Desktop: 3 columns */
  }
}
```

### Responsive Typography

**Fluid typography:**
```css
h1 {
  font-size: clamp(2rem, 5vw, 4rem);
}

p {
  font-size: clamp(1rem, 2vw, 1.125rem);
}
```

**Responsive scale:**
```css
/* Mobile */
h1 { font-size: 2rem; }
h2 { font-size: 1.5rem; }
p { font-size: 1rem; }

/* Desktop */
@media (min-width: 1024px) {
  h1 { font-size: 3rem; }
  h2 { font-size: 2rem; }
  p { font-size: 1.125rem; }
}
```

### Responsive Images

**Responsive image:**
```css
img {
  max-width: 100%;
  height: auto;
}
```

**Art direction:**
```jsx
<picture>
  <source
    media="(min-width: 1024px)"
    srcSet="desktop.jpg"
  />
  <source
    media="(min-width: 768px)"
    srcSet="tablet.jpg"
  />
  <img src="mobile.jpg" alt="Description" />
</picture>
```

**Responsive background:**
```css
.hero {
  background-image: url('mobile.jpg');
}

@media (min-width: 768px) {
  .hero {
    background-image: url('tablet.jpg');
  }
}

@media (min-width: 1024px) {
  .hero {
    background-image: url('desktop.jpg');
  }
}
```

### Container Queries

**Component-based responsive:**
```css
.card-container {
  container-type: inline-size;
}

.card {
  padding: 1rem;
}

@container (min-width: 400px) {
  .card {
    display: grid;
    grid-template-columns: 1fr 2fr;
  }
}
```

### Navigation Patterns

**Mobile menu:**
```jsx
function Navigation() {
  const [isOpen, setIsOpen] = useState(false);
  
  return (
    <nav>
      {/* Mobile: Hamburger */}
      <button
        className="md:hidden"
        onClick={() => setIsOpen(!isOpen)}
      >
        Menu
      </button>
      
      {/* Mobile: Drawer */}
      <div className={`
        fixed inset-0 bg-white transform transition-transform
        ${isOpen ? 'translate-x-0' : '-translate-x-full'}
        md:relative md:translate-x-0
      `}>
        <ul className="flex flex-col md:flex-row">
          <li>Home</li>
          <li>About</li>
          <li>Contact</li>
        </ul>
      </div>
    </nav>
  );
}
```

### Touch Targets

**Minimum size:**
```css
button, a {
  min-height: 44px; /* iOS recommendation */
  min-width: 44px;
  padding: 0.75rem 1rem;
}
```

**Touch-friendly spacing:**
```css
.button-group button {
  margin: 0.5rem; /* Space between touch targets */
}
```

### Viewport Units

**Full height:**
```css
.hero {
  height: 100vh; /* Viewport height */
  height: 100dvh; /* Dynamic viewport (mobile) */
}
```

**Responsive spacing:**
```css
.section {
  padding: 5vw; /* Scales with viewport */
}
```

### Common Patterns

**Sidebar layout:**
```css
.layout {
  display: grid;
  gap: 2rem;
  
  /* Mobile: Stack */
  grid-template-columns: 1fr;
}

@media (min-width: 1024px) {
  .layout {
    /* Desktop: Sidebar + main */
    grid-template-columns: 250px 1fr;
  }
}
```

**Card grid:**
```css
.cards {
  display: grid;
  gap: 1rem;
  
  /* Mobile: 1 column */
  grid-template-columns: 1fr;
}

@media (min-width: 640px) {
  .cards {
    /* Tablet: 2 columns */
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (min-width: 1024px) {
  .cards {
    /* Desktop: 3 columns */
    grid-template-columns: repeat(3, 1fr);
  }
}

@media (min-width: 1280px) {
  .cards {
    /* Large: 4 columns */
    grid-template-columns: repeat(4, 1fr);
  }
}
```

**Hero section:**
```css
.hero {
  padding: 2rem 1rem;
  text-align: center;
}

@media (min-width: 768px) {
  .hero {
    padding: 4rem 2rem;
  }
}

@media (min-width: 1024px) {
  .hero {
    padding: 6rem 3rem;
    text-align: left;
    display: grid;
    grid-template-columns: 1fr 1fr;
    align-items: center;
  }
}
```

### Testing

**Browser DevTools:**
- Chrome: Device toolbar (Cmd+Shift+M)
- Firefox: Responsive Design Mode
- Safari: Enter Responsive Design Mode

**Test on real devices:**
- iPhone (various sizes)
- Android phones
- Tablets
- Different browsers

**Responsive testing tools:**
- BrowserStack
- LambdaTest
- Responsively App

## Responsive Checklist

**Layout:**
- [ ] Mobile-first approach
- [ ] Flexible grid system
- [ ] No horizontal scrolling
- [ ] Content readable at all sizes

**Typography:**
- [ ] Readable font sizes (16px+ body)
- [ ] Appropriate line height
- [ ] Fluid typography for headings
- [ ] Proper text wrapping

**Images:**
- [ ] Responsive images
- [ ] Appropriate sizes for devices
- [ ] Fast loading
- [ ] Art direction where needed

**Navigation:**
- [ ] Mobile menu pattern
- [ ] Touch-friendly targets (44px+)
- [ ] Easy to use on mobile
- [ ] Accessible

**Performance:**
- [ ] Fast on mobile networks
- [ ] Optimized images
- [ ] Minimal layout shifts
- [ ] Touch interactions smooth

## Best Practices

**Use relative units:**
```css
/* Good */
padding: 1rem;
font-size: 1.125rem;
width: 80%;

/* Avoid */
padding: 16px;
font-size: 18px;
width: 800px;
```

**Test early and often:**
- Test on mobile first
- Use real devices
- Test different orientations
- Test with slow connections

**Progressive enhancement:**
- Core functionality works on all devices
- Enhanced features for larger screens
- Graceful degradation
```

