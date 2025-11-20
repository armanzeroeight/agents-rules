---
description: Audit web page or component for WCAG accessibility compliance
allowed-tools: Read, Grep
argument-hint: [file-or-url]
---

# Audit Accessibility

Audit web page or component for accessibility issues and WCAG compliance.

## Your Task

Perform accessibility audit and provide remediation recommendations.

### Arguments

- `$1`: File path or URL to audit

### Steps

1. **Read the code or page:**
   - Parse HTML/JSX structure
   - Identify interactive elements
   - Check for ARIA attributes

2. **Check critical issues:**

   **Missing alt text:**
   - Find all `<img>` tags
   - Check for alt attribute
   - Flag decorative images without alt=""

   **Form labels:**
   - Find all inputs
   - Check for associated labels
   - Verify aria-label or aria-labelledby

   **Keyboard navigation:**
   - Check for onClick on non-interactive elements
   - Verify tabIndex usage
   - Check for keyboard event handlers

   **Color contrast:**
   - Identify text and background colors
   - Calculate contrast ratios
   - Flag ratios below 4.5:1 (AA)

   **Heading hierarchy:**
   - Check heading levels (h1-h6)
   - Verify no skipped levels
   - Ensure single h1 per page

3. **Check ARIA usage:**
   - Verify valid ARIA attributes
   - Check for redundant ARIA
   - Validate ARIA roles

4. **Provide severity ratings:**

   **Critical (must fix):**
   - Missing alt text on images
   - Form inputs without labels
   - Keyboard traps
   - Color contrast below 3:1

   **High (should fix):**
   - Color contrast below 4.5:1
   - Missing ARIA labels on icon buttons
   - Improper heading hierarchy
   - Missing skip links

   **Medium (recommended):**
   - Missing lang attribute
   - No focus indicators
   - Redundant ARIA
   - Missing landmarks

5. **Provide remediation code:**

   For each issue, show before/after:
   ```jsx
   // Before
   <img src="logo.png" />
   
   // After
   <img src="logo.png" alt="Company Logo" />
   ```

6. **Generate accessibility report:**

   ```
   Accessibility Audit Report
   ==========================
   
   WCAG Level: AA
   
   Critical Issues (3):
   1. Missing alt text on 5 images
   2. Form input without label (email field)
   3. Button not keyboard accessible
   
   High Priority (4):
   1. Color contrast 3.2:1 on button (needs 4.5:1)
   2. Heading hierarchy skips from h1 to h3
   3. Icon button missing aria-label
   4. No skip link for keyboard users
   
   Medium Priority (2):
   1. Missing lang attribute on html
   2. Focus indicator not visible
   
   Compliance Score: 65/100
   Estimated fix time: 2-3 hours
   ```

7. **Provide testing recommendations:**
   - Run axe DevTools
   - Test with keyboard only
   - Test with screen reader (NVDA/VoiceOver)
   - Use Lighthouse accessibility audit

### Examples

**Example 1: Audit component file**
```
/audit-accessibility src/components/LoginForm.tsx
```

**Example 2: Audit page**
```
/audit-accessibility src/pages/Dashboard.tsx
```

**Example 3: Audit URL**
```
/audit-accessibility https://example.com
```

## Notes

- Focus on WCAG 2.1 Level AA compliance
- Prioritize critical issues first
- Provide specific code fixes
- Include testing tools recommendations
- Check semantic HTML usage
- Verify ARIA attributes are correct
- Test keyboard navigation paths
- Validate color contrast ratios
