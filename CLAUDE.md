# Sidebery Development Guide

## Build/Development Commands
- **Dev Mode**: `npm run dev` - Build and watch in development mode
- **Production**: `npm run build` - Full production build
- **Linting**: `npm run lint` - Run ESLint and web-ext lint
- **Running**: `npm run dev.run` - Run the extension in development mode
- **Cleanup**: `npm run clear` - Remove addon directory

## Code Style Guidelines
- **Formatting**: Uses Prettier with 2-space indent, no semicolons, single quotes
- **TypeScript**: Strict mode, custom types in `src/types/` directory
- **Imports**: Use absolute imports from `src/`, group by domain
- **File Organization**:
  - Services: Business logic in `src/services/`
  - Components: Vue components in `src/components/` and related subdirectories
  - Assets: SVG icons and resources in `src/assets/`

## Naming Conventions
- **Files**: kebab-case or dot-separated (`tabs.bg.actions.ts`)
- **Functions/Variables**: camelCase
- **Classes/Types**: PascalCase
- **Constants**: UPPERCASE_WITH_UNDERSCORES

## Vue Components
- Use Vue 3 Composition API with `<script setup>` syntax
- Templates in Pug syntax
- One component per file with `.vue` extension

## Error Handling
- Use the `Logs` service: `Logs.info()`, `Logs.warn()`, `Logs.err()`
- Include context in error messages (component name, IDs, etc.)

## Extension Architecture
- Background/foreground separation for browser extension model
- Service-based approach with dedicated modules
- Event-driven communication via message passing