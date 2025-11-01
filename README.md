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

## Status

🚧 In Progress - Initial structure only

