# UChicago DSI Cluster Information Site - Claude Reference

## Project Overview
This is a Jekyll-based documentation website for the University of Chicago Data Science Institute's GPU cluster. The site provides information about cluster resources, policies, tools, and usage guidelines.

## Technology Stack
- **Static Site Generator**: Jekyll 4.3.2
- **Theme**: Minimal Mistakes Jekyll theme
- **Ruby Version**: 3.1 (via Docker)
- **Containerization**: Docker + Make workflow
- **Deployment**: GitHub Pages

## Development Workflow - IMPORTANT
**ALL development work must be done through Make/Docker commands. Do not install or run anything directly in the local environment.**

### Available Make Commands
```bash
# Build the Docker image
make build

# Run the development server (builds image first)
make serve
# Site available at: http://localhost:4000

# Get interactive shell in container
make inter

# Run with debugging/trace information
make trace

# Clean up Docker resources
make clean

# Full rebuild (clean + build + serve)
make rebuild
```

### Docker Configuration
- **Base Image**: ruby:3.1-buster
- **Working Directory**: /site
- **Exposed Port**: 4000
- **Bundle Cache**: Uses Docker volume `jekyll_bundle_cache`
- **Live Reload**: Enabled by default

## Site Structure

### Content Collections
- `_pages/` - Main site pages
- `_policies/` - Usage guidelines and purchasing policies
- `_resources/` - Cluster information and monitoring
- `_tools/` - Documentation for cluster tools (e.g., dsiquota)
- `pages/` - Additional documentation pages organized by topic:
  - `quickstart/` - Account setup, SSH, client tools
  - `using-the-cluster/` - Environment management, containers, batch jobs
  - `advanced-topics/` - CUDA changes, file transfers
  - `faq/` - Troubleshooting and common errors
  - `contact/` - Contact information
  - `research/` - Affiliated research

### Configuration Files
- `_config.yml` - Main Jekyll configuration
- `_config_local.yml` - Local development overrides
- `_data/navigation.yml` - Site navigation structure
- `_data/publications.csv` - Research publications data

### Key Directories
- `assets/` - CSS, images, and static assets
- `_includes/` - Jekyll include templates
- `_layouts/` - Page layout templates
- `_site/` - Generated site output (auto-generated)
- `images/` - Site images and graphics

## Content Management

### Adding New Pages
1. Create markdown files in appropriate collection directories
2. Use proper frontmatter with layout and navigation settings
3. Update `_data/navigation.yml` if adding to main navigation

### Content Structure
- Most content follows the pattern of having both a main file and a `-content.md` file
- Navigation is centrally managed in `_data/navigation.yml`
- All pages use the `single` layout with sidebar navigation

## Dependencies & Gems
Key Ruby gems (see Gemfile):
- jekyll (~> 4.3.2)
- minimal-mistakes-jekyll (theme)
- jekyll-sitemap, jekyll-feed (SEO)
- jekyll-paginate, jekyll-gist
- webrick (Ruby 3.0+ compatibility)
- sass-embedded (CSS processing)

## Testing & Deployment
- **Local Testing**: Use `make serve` to test changes
- **Deployment**: Automatic via GitHub Pages on push to main branch
- **Cleaning**: Use `make clean` to reset Docker environment if needed

## Common Development Tasks

### Making Content Changes
1. Edit markdown files in appropriate directories
2. Use `make serve` to preview changes locally
3. Changes auto-reload with live reload enabled

### Debugging Issues
1. Use `make trace` for detailed Jekyll build information
2. Use `make inter` to get shell access in container
3. Check Jekyll logs in container for build errors

### Adding New Features
1. Update `_config.yml` for new collections or global settings
2. Modify `_data/navigation.yml` for navigation changes
3. Add layouts in `_layouts/` if needed
4. Test thoroughly with `make serve`

## Important Notes
- Never run Jekyll commands directly - always use Make/Docker
- Site uses UChicago branding (maroon color scheme)
- All documentation should be clear and accessible to researchers
- Follow existing content patterns and frontmatter structure
