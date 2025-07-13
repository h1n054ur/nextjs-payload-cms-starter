# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

```bash
# Development
pnpm dev                    # Start development server (http://localhost:3000)
pnpm build                  # Production build
pnpm start                  # Start production server
pnpm dev:prod               # Test production build locally

# Code Quality
pnpm lint                   # Run ESLint
pnpm lint:fix               # Fix ESLint issues automatically

# Testing
pnpm test                   # Run all tests (integration + e2e)
pnpm test:int               # Run integration tests with Vitest
pnpm test:e2e               # Run e2e tests with Playwright

# Payload Specific
pnpm payload                # Access Payload CLI
pnpm generate:types         # Generate TypeScript types from Payload schema
pnpm generate:importmap     # Generate import map for admin panel
```

## Architecture Overview

This is a **Payload Website Template** - a full-stack Next.js app with integrated CMS capabilities using Payload 3.47.0.

### Dual App Structure

The application has **two distinct apps** within `/src/app/`:

1. **`(frontend)/`** - Public website (pages, posts, search)
2. **`(payload)/`** - Admin panel and API routes (`/admin`, `/api`)

Both apps run on the same Next.js instance with shared components and utilities.

### Content Architecture

**Collections** (`/src/collections/`):
- **Pages**: Layout builder pages with flexible block system
- **Posts**: Blog posts with categories, authors, rich content
- **Media**: File uploads with automated image optimization
- **Categories**: Nested taxonomy using Nested Docs plugin
- **Users**: Authentication-enabled admin users

**Globals** (`/src/Header/`, `/src/Footer/`):
- Singleton content for site-wide elements
- Automatic revalidation hooks for frontend updates

### Block-Based Layout System

**Layout Builder** (`/src/blocks/`):
- Each collection (Pages/Posts) uses flexible block layouts
- Pre-built blocks: Archive, Banner, CallToAction, Code, Content, Form, Media
- Rendering handled by `/src/blocks/RenderBlocks.tsx`
- Custom blocks can be added by creating new block configs

**Hero System** (`/src/heros/`):
- Multiple hero variants: HighImpact, MediumImpact, LowImpact, PostHero
- Configured per-page via hero field selection

### Key Patterns

**Field Reusability** (`/src/fields/`):
- `slug/`: Auto-generated URL slugs with manual override
- `link.ts`: Reusable internal/external link field
- `linkGroup.ts`: Multiple link collections
- `defaultLexical.ts`: Standardized rich text editor config

**Access Control** (`/src/access/`):
- `anyone`: Public access patterns
- `authenticated`: Admin-only access
- `authenticatedOrPublished`: Public for published, authenticated for drafts

**Hooks & Lifecycle** (`/src/hooks/`):
- Collection hooks for data population and validation
- Page-specific revalidation hooks for Next.js cache management
- Auto-generated slugs and publish date population

### Plugin System

**Active Plugins** (`/src/plugins/index.ts`):
- SEO: Meta tags, Open Graph, structured data
- Search: Full-text search with server-side rendering
- Redirects: URL redirect management
- Form Builder: Dynamic form creation with validation
- Nested Docs: Hierarchical category structure

### Frontend Integration

**Theme System** (`/src/providers/Theme/`):
- Dark/light mode with persistence
- CSS variables for customization (`/src/cssVariables.js`)

**Component Library** (`/src/components/`):
- shadcn/ui components in `/src/components/ui/`
- Custom components: Media, RichText, AdminBar, CollectionArchive
- Reusable patterns for cards, pagination, forms

**Styling**:
- TailwindCSS with custom configuration
- Geist font family (Sans/Mono)
- Responsive design with mobile-first approach

### Database & Storage

**Current Setup**:
- SQLite for development (`payload-demo.db`)
- Media stored in `/public/media/` with automatic size generation
- Support for PostgreSQL/Vercel Postgres in production

**Type Safety**:
- Auto-generated types in `payload-types.ts`
- Run `pnpm generate:types` after schema changes

### Content Management Features

**Editorial Workflow**:
- Draft/published states with preview URLs
- Live preview with device breakpoints (mobile/tablet/desktop)
- Version history (up to 50 versions per document)
- Scheduled publishing via jobs queue

**SEO & Performance**:
- Automatic sitemap generation (`/pages-sitemap.xml`, `/posts-sitemap.xml`)
- On-demand revalidation for updated content
- Image optimization with Sharp (multiple sizes: thumbnail, small, medium, large, xlarge, og)

## Important Configuration Files

- `/src/payload.config.ts` - Main Payload configuration
- `/next.config.js` - Next.js + Payload integration
- `/tailwind.config.mjs` - TailwindCSS with shadcn/ui setup
- `/components.json` - shadcn/ui component configuration

## Testing Setup

**Integration Tests** (`/tests/int/`):
- Vitest with jsdom for API and component testing
- Configuration in `vitest.config.mts`

**E2E Tests** (`/tests/e2e/`):
- Playwright for full application testing
- Configuration in `playwright.config.ts`

## Development Notes

**Adding New Collections**:
1. Create collection config in `/src/collections/`
2. Register in `payload.config.ts`
3. Run `pnpm generate:types` to update TypeScript types

**Adding New Blocks**:
1. Create block config and component in `/src/blocks/`
2. Register in `/src/blocks/RenderBlocks.tsx`
3. Add to relevant collection's layout field

**Database Changes**:
- SQLite uses `push: true` for development (auto-sync schema)
- For production PostgreSQL, create migrations with `pnpm payload migrate:create`