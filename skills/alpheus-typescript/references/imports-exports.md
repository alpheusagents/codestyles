# Imports & Exports

## Imports

### Node builtins

`node:` prefix and `import * as Name` namespace form with `PascalCase` aliases: `Path`, `Fs`, `Fsp`, `Http`, `Os`.

Never `import path from "node:path"`.

```ts
import * as Fs from "node:fs";
import * as Fsp from "node:fs/promises";
import * as Os from "node:os";
import * as Path from "node:path";
```

### Path Aliases

Avoid relative paths like `./` or `../`. Always prefer the absolute `#/` alias for all intra-package imports.

`#/*` → `./src/*` is configured in every package's `tsconfig.json`:

```json
{
    "compilerOptions": {
        "paths": {
            "#/root/*": ["./*"],
            "#/*": ["./src/*"]
        }
    }
}
```

## Exports

### Named Only

Prefer named exports only; avoid `export default` except where a framework or runtime contract requires it (`export default app;` in `src/index.ts`).

### Declaration Without Export

Avoid inline `export const` or `export type` on the declaration, except for framework- or runtime-required exports (see below):

```ts
type Options = {};

type Result = {};

const main = (options?: Options): Result => {};

export type { Options, Result };
export { main };
```

### Framework-required Inline Exports

Framework or runtime contracts override both the named-exports preference and the inline-export rule when the contract requires a specific export form, including a default export, in a designated file. Keep the required declaration and export in the form and location the contract expects; do not move it to the grouped-export section. Use the normal form when the contract accepts it.

This exception applies only to the exports the contract requires. All other exports in the file follow the normal rules. A file whose only export is required by the contract ends after that export; do not add empty groups to compensate.

### Non-empty Groups Only

Write only the groups that have members. The braces are not placeholders to copy:

```ts
// ✓ types only
export type { Options, Result };

// ✓ values only
export { createThing };

// ✓ both
export type { Options, Result };
export { createThing };

// ✗ never
export type {};
export {};
```

A file with no named exports ends after its declarations. The one legitimate empty export is `export {};` in a side-effect-only file that has no imports and must be a module, such as a file that only contains `declare global`:

```ts
declare global {
    var foo: string;
}

export {};
```

Use it alone in that case; never add `export type {};`.

### Entrypoint Re-exports

In a library, `src/index.ts` or other entrypoints act as a barrel file to re-export types and values from internal files:

```ts
export type { Options } from "#/@types/options";
export { createXxx } from "#/functions/create";
```

### Renamed Re-exports for Public Ergonomics

For things like plugin routers, keep the local name generic and alias on export:

```ts
export { plugin as xxx };
export { plugin as pluginXxx };
export { router as routerXxx };
```

## See Also

- Structure: [`structure.md`](./structure.md)
- Types: [`types.md`](./types.md)
- Functions: [`functions.md`](./functions.md)
