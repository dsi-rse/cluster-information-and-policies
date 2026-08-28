# UChicago DSI Cluster Information Site

## Project Overview
Jekyll-based documentation website for the University of Chicago Data Science Institute's GPU cluster. Provides information about cluster resources, policies, tools, and usage guidelines.

- **Live site**: https://cluster-policy.ds.uchicago.edu/
- **Deployment**: GitHub Pages (automatic on push to main)

## Technology Stack
- Jekyll 4.3.2 with Minimal Mistakes theme
- Ruby 3.1 via Docker
- Docker + Make workflow

## Development Workflow

**All development must go through Make/Docker. Never run Jekyll directly.**

```bash
make build    # Build Docker image
make serve    # Dev server at http://localhost:4000
make inter    # Interactive shell in container
make trace    # Build with debug/trace output
make clean    # Remove Docker resources
make rebuild  # Full clean + build + serve
```

## Site Structure

### Content Locations
| Directory | Purpose |
|-----------|---------|
| `_pages/` | Main site pages |
| `_policies/` | Usage guidelines and purchasing policies |
| `_resources/` | Cluster information and monitoring |
| `_tools/` | Tool documentation (e.g., dsiquota) |
| `pages/quickstart/` | Account setup, SSH, client tools |
| `pages/using-the-cluster/` | Environments, containers, batch jobs |
| `pages/advanced-topics/` | CUDA changes, file transfers |
| `pages/faq/` | Troubleshooting and common errors |
| `pages/contact/` | Contact information |
| `pages/research/` | Affiliated research |

### Configuration
| File | Purpose |
|------|---------|
| `_config.yml` | Main Jekyll configuration |
| `_config_local.yml` | Local development overrides |
| `_data/navigation.yml` | Site navigation structure |
| `_data/publications.csv` | Research publications data |

### Layouts and Templates
| Directory | Purpose |
|-----------|---------|
| `_layouts/` | Page layout templates (home, single, collection, splash) |
| `_includes/` | Reusable template fragments |
| `assets/` | CSS, images, static assets |

## Content Conventions

- Pages use `single` layout with sidebar navigation
- Frontmatter includes: `title`, `layout`, `permalink`, `classes`, `nav_order`, `parent`, `category`
- Some pages use `toc: true` with `toc_sticky: true` for table of contents
- Header overlays use UChicago maroon: `#800000`
- Navigation is managed centrally in `_data/navigation.yml`
- Jekyll collections: `resources`, `policies`, `tools`, `howto`

## Adding / Editing Content

1. Create or edit markdown files in the appropriate directory
2. Include proper frontmatter (see existing pages for patterns)
3. Update `_data/navigation.yml` if adding to main navigation
4. Test with `make serve`
5. Push to main to deploy

## Dependencies
Key gems: jekyll (~> 4.3.2), minimal-mistakes-jekyll, jekyll-sitemap, jekyll-feed, jekyll-paginate, webrick, sass-embedded. See `Gemfile` for full list.
