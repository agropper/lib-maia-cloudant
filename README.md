# lib-maia-cloudant

Clean Cloudant client for MAIA (Medical AI Assistant).

## Purpose

Extract Cloudant document and session operations from the monolithic `maia-cloud-clean/server.js` into a simple, reusable library with no cache manager complexity.

## Features

- **Document operations**: getDocument, saveDocument, queryView
- **Session store**: express-session compatible Cloudant store
- **Simple API**: Promise-based functions with clear error handling
- **No cache manager**: Direct Cloudant calls only

## Installation

### Development (symlink)
```bash
npm link
```

### Production (npm)
```bash
npm install lib-maia-cloudant
```

## Usage

```javascript
import { CloudantClient } from 'lib-maia-cloudant';
import { CloudantSessionStore } from 'lib-maia-cloudant/session';

// Initialize
const client = new CloudantClient(process.env.CLOUDANT_URL);

// Get document
const user = await client.getDocument('maia_users', 'userId123');

// Save document
await client.saveDocument('maia_users', userDoc);

// Session store
const sessionStore = new CloudantSessionStore(client);
app.use(session({
  store: sessionStore,
  // ... session config
}));
```

## Development

Extract from:
- `maia-cloud-clean/src/utils/couchdb-client.js`
- `maia-cloud-clean/src/utils/couchdb-session-store.js`
- `maia-cloud-clean/inventory/backend-utilities-outline.md`

## Architecture

- **No cache manager**: Each call hits Cloudant directly
- **No complex state**: Simple document operations
- **Production logging**: Winston INFO/WARN/ERROR only

## Features

- **Document operations**: `getDocument`, `saveDocument`, `deleteDocument`, `findDocuments`, `getAll`
- **Session store**: `express-session` compatible `CloudantSessionStore`
- **Session management**: Sessions stored by `userId` as `_id` for easy dashboard viewing
- **Audit logging**: `AuditLogService` for security event logging
- **Database creation**: Automatic database creation helper
- **Simple API**: Promise-based functions with clear error handling

## API Reference

### CloudantClient

```javascript
const client = new CloudantClient({
  url: process.env.CLOUDANT_URL,
  username: process.env.CLOUDANT_USERNAME,
  password: process.env.CLOUDANT_PASSWORD
});

// Test connection
await client.testConnection();

// Create database
await client.createDatabase('maia_users');

// Document operations
const doc = await client.getDocument('maia_users', 'userId');
await client.saveDocument('maia_users', doc);
await client.deleteDocument('maia_users', 'userId');

// Query
const results = await client.findDocuments('maia_users', {
  selector: { type: 'user' }
});
```

### CloudantSessionStore

```javascript
const sessionStore = new CloudantSessionStore({
  cloudantClient: client,
  dbName: 'maia_sessions'
});

app.use(session({
  store: sessionStore,
  secret: 'your-secret',
  resave: false,
  saveUninitialized: false
}));
```

**Session Storage**: Sessions are stored with `userId` as the document `_id`, making them easy to view in the Cloudant dashboard. A small mapping document is maintained for express-session compatibility.

### AuditLogService

```javascript
const auditLog = new AuditLogService(client, 'maia_audit_log');

// Log security events
await auditLog.logEvent({
  type: 'login_success',
  userId: 'user123',
  ip: '192.168.1.1',
  userAgent: 'Mozilla/5.0...'
});

// Query logs
const userLogs = await auditLog.getUserLogs('user123', 50);
const failures = await auditLog.getFailedLogins(20);
```

## Status

✅ **Complete** - Fully implemented and tested

