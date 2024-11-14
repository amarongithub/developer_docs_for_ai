# Svelte 5 Essential Documentation

## 1. Runes Overview
Runes are function-like symbols with a $ prefix that provide instructions to the Svelte compiler.

### Core Runes

#### $state

```markdown
```ts
let count = $state(0);                  // Simple state
let user = $state({ name: 'Alice' });   // Object state
let items = $state([1, 2, 3]);         // Array state
```
- Creates reactive state
- Can be used anywhere, not just component top-level
- Deep reactivity for objects and arrays
- Use `$state.raw()` for non-deep reactivity

#### $derived
```ts
let count = $state(0);
let doubled = $derived(count * 2);

// Complex derivations
let total = $derived.by(() => {
  let sum = 0;
  for (const n of numbers) sum += n;
  return sum;
});
```
- Replaces `$:` reactive declarations
- More explicit and predictable
- Can be used anywhere in code

#### $effect
```ts
$effect(() => {
  // Runs after DOM updates
  console.log('count is now:', count);
  
  return () => {
    // Cleanup function (optional)
    console.log('cleaning up');
  };
});

// Pre-update effects
$effect.pre(() => {
  // Runs before DOM updates
});
```
- Replaces `$:` statements for side effects
- Clearer intention than `$:`
- Better cleanup handling

### $props
```ts
<script lang="ts">
  interface Props {
    name: string;
    age?: number;
    onSave?: (data: any) => void;
  }
  
  let { name, age = 21, onSave } = $props<Props>();
</script>
```
- Replaces `export let`
- More consistent with standard JS destructuring
- Better TypeScript integration

## 2. New Event Handling

### DOM Events
```svelte
<!-- Old -->
<button on:click={handler}>Click</button>

<!-- New -->
<button onclick={handler}>Click</button>

<!-- With multiple handlers -->
<button onclick={(e) => {
  handler1(e);
  handler2(e);
}}>Click</button>
```

### Component Events
```svelte
<!-- Child.svelte -->
<script lang="ts">
  let { onSave, onChange } = $props<{
    onSave: (data: any) => void;
    onChange?: (value: string) => void;
  }>();
</script>

<!-- Parent.svelte -->
<Child 
  onSave={handleSave}
  onChange={handleChange}
/>
```
- Moves away from `createEventDispatcher`
- Uses callback props instead of custom events
- More aligned with standard web patterns

## 3. Snippets (Replacing Slots)

### Basic Usage
```svelte
<!-- Parent.svelte -->
<Modal>
  {#snippet header()}
    <h2>Welcome</h2>
  {/snippet}
  
  {#snippet content()}
    <p>Main content here</p>
  {/snippet}
</Modal>

<!-- Modal.svelte -->
<script lang="ts">
  let { header, content } = $props<{
    header: Snippet;
    content: Snippet;
  }>();
</script>

<div class="modal">
  {@render header()}
  {@render content()}
</div>
```

### Snippets with Parameters
```svelte
<!-- Parent.svelte -->
<List items={data}>
  {#snippet item(data: ItemType)}
    <div class="item">{data.name}</div>
  {/snippet}
</List>
```

## 4. Component Lifecycle Changes

### Mounting Components
```ts
import { mount, unmount } from 'svelte';

// Mount
const app = mount(App, {
  target: document.body,
  props: { initial: 'data' }
});

// Unmount
unmount(app);
```

### Effect Timing
```svelte
<script lang="ts">
  // Runs after mount and prop changes
  $effect(() => {
    // Component is mounted
    return () => {
      // Cleanup before unmount or re-run
    };
  });

  // Runs before DOM updates
  $effect.pre(() => {
    // Access DOM before updates
  });
</script>
```

## 5. Important Migration Notes

### Key Changes
- No more `$:` reactive declarations
- No more `export let` for props
- Event handling uses standard HTML attributes
- Slots replaced by snippets
- Components are functions, not classes
- All state changes are tracked at runtime

### Compatibility Mode
```js
// svelte.config.js
export default {
  compilerOptions: {
    runes: true, // Enable runes mode
    legacy: {
      componentApi: true // For compatibility
    }
  }
};
```