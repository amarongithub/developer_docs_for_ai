# SvelteKit Advanced & Lesser-Known Features Reference

## 1. Shallow Routing
- New feature allowing creation of history entries without navigation
- Useful for modal dialogs that can be dismissed via back button
- Uses `pushState` and `replaceState` functions
- Can associate state with history entries via `$page.state`
- State is lost on page reload/direct URL access

```js
// Example of shallow routing for modal
import { pushState } from '$app/navigation';

function showModal() {
  pushState('', {
    showModal: true 
  });
}
```

## 2. Environment Variable Prefixes
- New in SvelteKit 1.21.0: `privatePrefix` configuration
- Controls which env vars are considered private/unsafe for client code
- Variables matching neither public nor private prefix are discarded
- Affects both static and dynamic env modules

```js
// svelte.config.js
export default {
  kit: {
    env: {
      privatePrefix: 'PRIVATE_', // New option
      publicPrefix: 'PUBLIC_'
    }
  }
}
```

## 3. Streaming with Promises 
- Server load functions can return promises that stream to browser
- Useful for slow/non-essential data loading
- Enables progressive loading patterns
- Requires careful error handling

```js
export async function load() {
  return {
    fastData: await getFastData(),
    slowData: getSlowData() // Returns promise that resolves later
  };
}
```

## 4. Advanced CSP Configuration
- Detailed Content Security Policy controls
- Supports both standard CSP and report-only mode
- Can use nonces or hashes for inline scripts
- Auto-handles SvelteKit-generated content

```js
// svelte.config.js
export default {
  kit: {
    csp: {
      mode: 'auto',
      directives: {
        'script-src': ['self']
      },
      reportOnly: {
        'script-src': ['self'],
        'report-uri': ['/csp-report']
      }
    }
  }
}
```

## 5. Output Strategy Configuration
- Controls how JavaScript modules are loaded
- Three strategies available:
  - `modulepreload` (default)
  - `preload-js`
  - `preload-mjs`
- Different browser compatibility implications

```js
// svelte.config.js
export default {
  kit: {
    output: {
      preloadStrategy: 'modulepreload' // or 'preload-js' or 'preload-mjs'
    }
  }
}
```

## 6. Advanced Service Worker Integration
- Granular control over service worker registration
- Can customize which static files are available
- Built-in version management
- Streams API support

```js
// svelte.config.js
export default {
  kit: {
    serviceWorker: {
      register: true,
      files: (filepath) => !/\.DS_Store/.test(filepath)
    }
  }
}
```

## 7. Request Event Properties
Lesser-known properties available in request events:

- `isDataRequest`: Identifies client-side data requests
- `isSubRequest`: Identifies same-origin fetch calls
- `getClientAddress()`: Gets client IP with adapter support
- `platform`: Adapter-specific context data

## 8. Advanced Error Handling
- Different strategies for prerender errors
- Customizable error handling for:
  - HTTP errors
  - Missing IDs
  - Entry generator mismatches
- Support for custom error handlers

```js
// svelte.config.js
export default {
  kit: {
    prerender: {
      handleHttpError: 'warn',
      handleMissingId: 'ignore',
      handleEntryGeneratorMismatch: ({ entry, message }) => {
        console.warn(`Entry mismatch: ${entry} - ${message}`);
      }
    }
  }
}
```

## 9. TypeScript Integration Features
- Generated route types via `$types`
- Ambient type declarations in `app.d.ts`
- Config manipulation via `typescript.config`
- Support for custom path aliases

## 10. Version Management
- Built-in support for app versioning
- Automatic version change detection
- Configurable polling for updates
- Integration with navigation lifecycle

```js
// svelte.config.js
export default {
  kit: {
    version: {
      name: 'v1.0.0',
      pollInterval: 60000 // Check every minute
    }
  }
}
```