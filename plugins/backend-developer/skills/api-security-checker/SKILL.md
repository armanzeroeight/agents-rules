---
name: api-security-checker
description: Audit API security for OWASP Top 10 vulnerabilities, authentication issues, and authorization flaws. Use when securing APIs, fixing security vulnerabilities, or implementing security best practices.
---

# API Security Checker

Audit API security and identify vulnerabilities based on OWASP Top 10.

## Quick Start

Check authentication, validate inputs, prevent SQL injection, implement rate limiting, use HTTPS.

## Instructions

### OWASP Top 10 for APIs

**1. Broken Object Level Authorization:**
```javascript
// Bad: No authorization check
app.get('/api/users/:id', (req, res) => {
  const user = await User.findById(req.params.id);
  res.json(user);
});

// Good: Check ownership
app.get('/api/users/:id', auth, async (req, res) => {
  if (req.user.id !== req.params.id && !req.user.isAdmin) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  const user = await User.findById(req.params.id);
  res.json(user);
});
```

**2. Broken Authentication:**
```javascript
// Bad: Weak password requirements
const isValidPassword = (password) => password.length >= 6;

// Good: Strong requirements
const isValidPassword = (password) => {
  return password.length >= 12 &&
    /[A-Z]/.test(password) &&
    /[a-z]/.test(password) &&
    /[0-9]/.test(password) &&
    /[^A-Za-z0-9]/.test(password);
};

// Implement rate limiting
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // 5 attempts
  message: 'Too many login attempts'
});

app.post('/api/login', loginLimiter, loginHandler);
```

**3. Excessive Data Exposure:**
```javascript
// Bad: Exposing sensitive data
app.get('/api/users/:id', async (req, res) => {
  const user = await User.findById(req.params.id);
  res.json(user); // Includes password hash, email, etc.
});

// Good: Return only necessary fields
app.get('/api/users/:id', async (req, res) => {
  const user = await User.findById(req.params.id)
    .select('id username avatar');
  res.json(user);
});
```

**4. Lack of Resources & Rate Limiting:**
```javascript
const rateLimit = require('express-rate-limit');

const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  standardHeaders: true,
  legacyHeaders: false,
});

app.use('/api/', apiLimiter);
```

**5. Broken Function Level Authorization:**
```javascript
// Bad: No role check
app.delete('/api/users/:id', auth, deleteUser);

// Good: Check admin role
app.delete('/api/users/:id', auth, requireAdmin, deleteUser);

function requireAdmin(req, res, next) {
  if (!req.user.isAdmin) {
    return res.status(403).json({ error: 'Admin access required' });
  }
  next();
}
```

**6. Mass Assignment:**
```javascript
// Bad: Accepting all fields
app.put('/api/users/:id', async (req, res) => {
  await User.update(req.params.id, req.body); // Can set isAdmin!
});

// Good: Whitelist fields
app.put('/api/users/:id', async (req, res) => {
  const { name, email, avatar } = req.body;
  await User.update(req.params.id, { name, email, avatar });
});
```

**7. Security Misconfiguration:**
```javascript
// Set security headers
const helmet = require('helmet');
app.use(helmet());

// Disable X-Powered-By
app.disable('x-powered-by');

// CORS configuration
const cors = require('cors');
app.use(cors({
  origin: process.env.ALLOWED_ORIGINS.split(','),
  credentials: true
}));
```

**8. Injection:**
```javascript
// Bad: SQL injection vulnerable
const query = `SELECT * FROM users WHERE email = '${email}'`;

// Good: Parameterized query
const query = 'SELECT * FROM users WHERE email = $1';
const result = await db.query(query, [email]);

// Input validation
const { body, validationResult } = require('express-validator');

app.post('/api/users',
  body('email').isEmail().normalizeEmail(),
  body('name').trim().escape(),
  async (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    // Process request
  }
);
```

**9. Improper Assets Management:**
```javascript
// Document API versions
// Deprecate old versions
// Remove unused endpoints

// Version API
app.use('/api/v1', v1Routes);
app.use('/api/v2', v2Routes);

// Deprecation header
app.use('/api/v1', (req, res, next) => {
  res.set('Deprecation', 'true');
  res.set('Sunset', 'Sat, 31 Dec 2024 23:59:59 GMT');
  next();
});
```

**10. Insufficient Logging & Monitoring:**
```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

// Log security events
app.post('/api/login', async (req, res) => {
  try {
    const user = await authenticate(req.body);
    logger.info('Login successful', { userId: user.id, ip: req.ip });
    res.json({ token: generateToken(user) });
  } catch (error) {
    logger.warn('Login failed', { email: req.body.email, ip: req.ip });
    res.status(401).json({ error: 'Invalid credentials' });
  }
});
```

### Authentication Security

**JWT best practices:**
```javascript
const jwt = require('jsonwebtoken');

// Generate token
const generateToken = (user) => {
  return jwt.sign(
    { id: user.id, email: user.email },
    process.env.JWT_SECRET,
    { expiresIn: '15m' } // Short expiry
  );
};

// Generate refresh token
const generateRefreshToken = (user) => {
  return jwt.sign(
    { id: user.id },
    process.env.REFRESH_SECRET,
    { expiresIn: '7d' }
  );
};

// Verify token
const verifyToken = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
};
```

**Password hashing:**
```javascript
const bcrypt = require('bcrypt');

// Hash password
const hashPassword = async (password) => {
  const salt = await bcrypt.genSalt(12);
  return bcrypt.hash(password, salt);
};

// Verify password
const verifyPassword = async (password, hash) => {
  return bcrypt.compare(password, hash);
};
```

### Input Validation

**Sanitize inputs:**
```javascript
const validator = require('validator');

// Email validation
if (!validator.isEmail(email)) {
  return res.status(400).json({ error: 'Invalid email' });
}

// URL validation
if (!validator.isURL(url)) {
  return res.status(400).json({ error: 'Invalid URL' });
}

// Escape HTML
const sanitized = validator.escape(userInput);
```

**Schema validation:**
```javascript
const Joi = require('joi');

const userSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().min(12).required(),
  name: Joi.string().min(2).max(50).required()
});

app.post('/api/users', async (req, res) => {
  const { error, value } = userSchema.validate(req.body);
  if (error) {
    return res.status(400).json({ error: error.details[0].message });
  }
  // Process validated data
});
```

### Authorization

**Role-Based Access Control (RBAC):**
```javascript
const roles = {
  user: ['read:own'],
  admin: ['read:any', 'write:any', 'delete:any']
};

const checkPermission = (permission) => {
  return (req, res, next) => {
    const userPermissions = roles[req.user.role] || [];
    if (!userPermissions.includes(permission)) {
      return res.status(403).json({ error: 'Insufficient permissions' });
    }
    next();
  };
};

app.delete('/api/users/:id',
  auth,
  checkPermission('delete:any'),
  deleteUser
);
```

### HTTPS and Transport Security

**Force HTTPS:**
```javascript
app.use((req, res, next) => {
  if (req.header('x-forwarded-proto') !== 'https' && process.env.NODE_ENV === 'production') {
    res.redirect(`https://${req.header('host')}${req.url}`);
  } else {
    next();
  }
});
```

**Security headers:**
```javascript
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", 'data:', 'https:']
    }
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  }
}));
```

### CORS Configuration

```javascript
const corsOptions = {
  origin: (origin, callback) => {
    const allowedOrigins = process.env.ALLOWED_ORIGINS.split(',');
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
  optionsSuccessStatus: 200
};

app.use(cors(corsOptions));
```

### API Key Security

```javascript
const apiKeyAuth = (req, res, next) => {
  const apiKey = req.header('X-API-Key');
  
  if (!apiKey) {
    return res.status(401).json({ error: 'API key required' });
  }
  
  // Verify API key (use hashed comparison)
  const isValid = await verifyApiKey(apiKey);
  if (!isValid) {
    return res.status(401).json({ error: 'Invalid API key' });
  }
  
  next();
};
```

## Security Checklist

**Authentication:**
- [ ] Strong password requirements
- [ ] Password hashing (bcrypt, argon2)
- [ ] JWT with short expiry
- [ ] Refresh token mechanism
- [ ] Rate limiting on auth endpoints
- [ ] MFA for sensitive operations

**Authorization:**
- [ ] Check user permissions
- [ ] Validate resource ownership
- [ ] Implement RBAC or ABAC
- [ ] Principle of least privilege

**Input Validation:**
- [ ] Validate all inputs
- [ ] Sanitize user data
- [ ] Use parameterized queries
- [ ] Whitelist allowed fields
- [ ] Validate file uploads

**Transport Security:**
- [ ] HTTPS only
- [ ] Security headers (Helmet)
- [ ] CORS configured properly
- [ ] HSTS enabled

**API Security:**
- [ ] Rate limiting
- [ ] API versioning
- [ ] Error handling (no stack traces)
- [ ] Logging security events
- [ ] API documentation

**Data Protection:**
- [ ] Encrypt sensitive data
- [ ] Secure session storage
- [ ] No secrets in code
- [ ] Environment variables
- [ ] Regular security audits

## Best Practices

**Defense in depth:**
- Multiple security layers
- Fail securely
- Least privilege
- Regular updates

**Secure defaults:**
- HTTPS required
- Strong authentication
- Input validation
- Rate limiting enabled

**Monitoring:**
- Log security events
- Monitor for anomalies
- Alert on suspicious activity
- Regular security audits
