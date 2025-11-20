---
name: accessibility-checker
description: Validate WCAG compliance, check screen reader support, and audit accessibility issues. Use when ensuring accessibility, fixing WCAG violations, or implementing inclusive design.
---

# Accessibility Checker

Validate web accessibility and ensure WCAG compliance.

## Quick Start

Use semantic HTML, add ARIA labels, ensure keyboard navigation, test color contrast, validate with axe or Lighthouse.

## Instructions

### WCAG Compliance Levels

**Level A (minimum):**
- Basic accessibility
- Must meet for legal compliance

**Level AA (recommended):**
- Standard for most websites
- Includes contrast requirements
- Government sites requirement

**Level AAA (enhanced):**
- Highest level
- Not required for all content
- Best practices

### Common Accessibility Issues

**1. Missing alt text:**
```jsx
// Bad
<img src="logo.png" />

// Good
<img src="logo.png" alt="Company Logo" />

// Decorative images
<img src="decoration.png" alt="" />
```

**2. Poor color contrast:**
```css
/* Bad: 2.5:1 contrast */
color: #777;
background: #fff;

/* Good: 4.5:1 contrast (AA) */
color: #595959;
background: #fff;

/* Better: 7:1 contrast (AAA) */
color: #333;
background: #fff;
```

**3. Missing form labels:**
```jsx
// Bad
<input type="text" placeholder="Name" />

// Good
<label htmlFor="name">Name</label>
<input type="text" id="name" />

// Or with aria-label
<input type="text" aria-label="Name" />
```

**4. No keyboard navigation:**
```jsx
// Bad: onClick on div
<div onClick={handleClick}>Click me</div>

// Good: Use button
<button onClick={handleClick}>Click me</button>

// Or make div focusable
<div
  role="button"
  tabIndex={0}
  onClick={handleClick}
  onKeyPress={(e) => e.key === 'Enter' && handleClick()}
>
  Click me
</div>
```

**5. Missing ARIA labels:**
```jsx
// Bad: Icon button without label
<button><CloseIcon /></button>

// Good: Add aria-label
<button aria-label="Close dialog">
  <CloseIcon />
</button>
```

### Semantic HTML

**Use proper elements:**
```jsx
// Bad
<div onClick={handleClick}>Submit</div>

// Good
<button onClick={handleClick}>Submit</button>

// Bad
<div className="heading">Title</div>

// Good
<h1>Title</h1>
```

**Heading hierarchy:**
```jsx
// Bad: Skipping levels
<h1>Page Title</h1>
<h3>Section</h3>

// Good: Proper hierarchy
<h1>Page Title</h1>
<h2>Section</h2>
<h3>Subsection</h3>
```

**Landmarks:**
```jsx
<header>
  <nav aria-label="Main navigation">
    {/* Navigation links */}
  </nav>
</header>

<main>
  <article>
    {/* Main content */}
  </article>
  <aside>
    {/* Sidebar */}
  </aside>
</main>

<footer>
  {/* Footer content */}
</footer>
```

### ARIA Attributes

**aria-label:**
```jsx
<button aria-label="Close">
  <X />
</button>
```

**aria-labelledby:**
```jsx
<h2 id="dialog-title">Confirm Action</h2>
<div role="dialog" aria-labelledby="dialog-title">
  {/* Dialog content */}
</div>
```

**aria-describedby:**
```jsx
<input
  type="password"
  aria-describedby="password-hint"
/>
<span id="password-hint">
  Must be at least 8 characters
</span>
```

**aria-live:**
```jsx
// Announce updates to screen readers
<div aria-live="polite" aria-atomic="true">
  {statusMessage}
</div>

// For urgent updates
<div aria-live="assertive">
  {errorMessage}
</div>
```

**aria-expanded:**
```jsx
<button
  aria-expanded={isOpen}
  aria-controls="menu"
  onClick={toggleMenu}
>
  Menu
</button>
<div id="menu" hidden={!isOpen}>
  {/* Menu items */}
</div>
```

### Keyboard Navigation

**Tab order:**
```jsx
// Use tabIndex to control focus order
<button tabIndex={0}>First</button>
<button tabIndex={0}>Second</button>
<button tabIndex={-1}>Not in tab order</button>
```

**Focus management:**
```jsx
function Dialog({ onClose }) {
  const closeButtonRef = useRef();
  
  useEffect(() => {
    // Focus close button when dialog opens
    closeButtonRef.current?.focus();
    
    // Trap focus in dialog
    const handleTab = (e) => {
      if (e.key === 'Tab') {
        // Implement focus trap
      }
    };
    
    document.addEventListener('keydown', handleTab);
    return () => document.removeEventListener('keydown', handleTab);
  }, []);
  
  return (
    <div role="dialog" aria-modal="true">
      <button ref={closeButtonRef} onClick={onClose}>
        Close
      </button>
    </div>
  );
}
```

**Keyboard shortcuts:**
```jsx
useEffect(() => {
  const handleKeyPress = (e) => {
    if (e.key === 'Escape') {
      closeDialog();
    }
    if (e.key === '/' && e.ctrlKey) {
      openSearch();
    }
  };
  
  document.addEventListener('keydown', handleKeyPress);
  return () => document.removeEventListener('keydown', handleKeyPress);
}, []);
```

### Screen Reader Support

**Skip links:**
```jsx
<a href="#main-content" className="skip-link">
  Skip to main content
</a>

<main id="main-content">
  {/* Content */}
</main>

// CSS
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  background: #000;
  color: #fff;
  padding: 8px;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
```

**Visually hidden text:**
```jsx
// CSS
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}

// Usage
<button>
  <TrashIcon />
  <span className="sr-only">Delete item</span>
</button>
```

**Announce dynamic content:**
```jsx
function Toast({ message }) {
  return (
    <div
      role="status"
      aria-live="polite"
      aria-atomic="true"
    >
      {message}
    </div>
  );
}
```

### Color Contrast

**Check contrast ratios:**
- Normal text: 4.5:1 (AA), 7:1 (AAA)
- Large text (18pt+): 3:1 (AA), 4.5:1 (AAA)
- UI components: 3:1

**Tools:**
- WebAIM Contrast Checker
- Chrome DevTools
- Lighthouse

**Don't rely on color alone:**
```jsx
// Bad: Only color indicates error
<input style={{ borderColor: 'red' }} />

// Good: Color + icon + text
<div>
  <input aria-invalid="true" aria-describedby="error" />
  <span id="error">
    <ErrorIcon /> Email is required
  </span>
</div>
```

### Forms Accessibility

**Labels:**
```jsx
<label htmlFor="email">Email</label>
<input
  type="email"
  id="email"
  required
  aria-required="true"
/>
```

**Error messages:**
```jsx
<input
  type="email"
  aria-invalid={hasError}
  aria-describedby={hasError ? "email-error" : undefined}
/>
{hasError && (
  <span id="email-error" role="alert">
    Please enter a valid email
  </span>
)}
```

**Fieldsets:**
```jsx
<fieldset>
  <legend>Shipping Address</legend>
  <label htmlFor="street">Street</label>
  <input type="text" id="street" />
</fieldset>
```

### Testing Tools

**Automated testing:**
```bash
# Install axe-core
npm install --save-dev @axe-core/react

# Use in tests
import { axe, toHaveNoViolations } from 'jest-axe';
expect.extend(toHaveNoViolations);

test('should have no accessibility violations', async () => {
  const { container } = render(<MyComponent />);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

**Browser extensions:**
- axe DevTools
- WAVE
- Lighthouse

**Screen readers:**
- NVDA (Windows, free)
- JAWS (Windows)
- VoiceOver (Mac, built-in)
- TalkBack (Android)

### Common Patterns

**Modal dialog:**
```jsx
function Modal({ isOpen, onClose, title, children }) {
  const modalRef = useRef();
  
  useEffect(() => {
    if (isOpen) {
      modalRef.current?.focus();
      document.body.style.overflow = 'hidden';
    }
    return () => {
      document.body.style.overflow = '';
    };
  }, [isOpen]);
  
  if (!isOpen) return null;
  
  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
      ref={modalRef}
      tabIndex={-1}
    >
      <h2 id="modal-title">{title}</h2>
      {children}
      <button onClick={onClose} aria-label="Close dialog">
        Close
      </button>
    </div>
  );
}
```

**Dropdown menu:**
```jsx
function Dropdown({ label, items }) {
  const [isOpen, setIsOpen] = useState(false);
  
  return (
    <div>
      <button
        aria-haspopup="true"
        aria-expanded={isOpen}
        onClick={() => setIsOpen(!isOpen)}
      >
        {label}
      </button>
      {isOpen && (
        <ul role="menu">
          {items.map(item => (
            <li key={item.id} role="menuitem">
              <button onClick={item.onClick}>
                {item.label}
              </button>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

**Tabs:**
```jsx
function Tabs({ tabs }) {
  const [activeTab, setActiveTab] = useState(0);
  
  return (
    <div>
      <div role="tablist">
        {tabs.map((tab, index) => (
          <button
            key={tab.id}
            role="tab"
            aria-selected={activeTab === index}
            aria-controls={`panel-${tab.id}`}
            onClick={() => setActiveTab(index)}
          >
            {tab.label}
          </button>
        ))}
      </div>
      {tabs.map((tab, index) => (
        <div
          key={tab.id}
          role="tabpanel"
          id={`panel-${tab.id}`}
          hidden={activeTab !== index}
        >
          {tab.content}
        </div>
      ))}
    </div>
  );
}
```

## Accessibility Checklist

**Perceivable:**
- [ ] All images have alt text
- [ ] Color contrast meets WCAG AA (4.5:1)
- [ ] Text can be resized to 200%
- [ ] Content is not conveyed by color alone

**Operable:**
- [ ] All functionality available via keyboard
- [ ] No keyboard traps
- [ ] Skip links provided
- [ ] Focus indicators visible

**Understandable:**
- [ ] Page language specified
- [ ] Labels for form inputs
- [ ] Error messages clear
- [ ] Consistent navigation

**Robust:**
- [ ] Valid HTML
- [ ] ARIA used correctly
- [ ] Works with assistive technologies
- [ ] No console errors

## Best Practices

**Start accessible:**
- Use semantic HTML first
- Add ARIA only when needed
- Test with keyboard
- Test with screen reader

**Progressive enhancement:**
- Core functionality works without JS
- Enhanced experience with JS
- Graceful degradation

**Regular testing:**
- Automated tests (axe)
- Manual keyboard testing
- Screen reader testing
- User testing with disabled users
