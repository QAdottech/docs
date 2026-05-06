# QA.tech Documentation

This is a Mintlify-powered documentation site for QA.tech.

## Cursor Cloud specific instructions

### Services

| Service | Command | Port |
|---------|---------|------|
| Mintlify Dev Server | `npm run start` | 6005 |

### Running the dev server

```bash
npm run start
```

This starts the Mintlify dev server on `http://localhost:6005`. The server hot-reloads when MDX files are edited.

### Key notes

- The site configuration lives in `mint.json` (navigation, theming, redirects).
- Documentation pages are `.mdx` files organized in subdirectories (`core-concepts/`, `best-practices/`, `configuration/`, etc.).
- When adding a new page, you must also add it to the `navigation` array in `mint.json`.
- There are no lint or test commands configured in this repo. Validation is done by running the dev server and verifying pages render correctly.
- If `mintlify dev` fails to start, run `npx mintlify install` to re-install the Mintlify framework dependencies.
- The initial server startup extracts the Mintlify framework which takes ~15-30 seconds before the preview is ready.
