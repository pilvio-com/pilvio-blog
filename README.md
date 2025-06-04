# pilvio-blog
# Pilvio Blog Content Repository

This repository contains the blog content for the Pilvio blog, which is automatically fetched and displayed on the main Pilvio website. All blog posts are written in Markdown format with YAML frontmatter.

## 📁 Repository Structure

```
posts/
├── kubernetes-alustamine.md
├── terraform-parimad-praktikad.md
├── aws-lambda-serverless.md
└── ...
```

All blog posts must be placed in the `posts/` directory as `.md` files.

## 📝 Content Format

### Frontmatter Requirements

Every blog post must include YAML frontmatter at the beginning of the file. Here are all supported fields:

```yaml
---
title: "Your Blog Post Title"           # Required: Post title
author: "Author Name"                   # Required: Author name
publishedAt: "15. november 2024"        # Required: Estonian format date
category: "Kubernetes"                  # Required: Post category
tags: ["kubernetes", "devops"]          # Required: Array of tags
featured: false                         # Required: Boolean (only one post should be true)
excerpt: "Brief description..."         # Optional: Custom excerpt (auto-generated if not provided)
readingTime: "8 min"                   # Optional: Reading time (auto-calculated if not provided)
imageUrl: "https://example.com/img.jpg" # Optional: Featured image URL
slug: "custom-slug"                    # Optional: Custom URL slug (auto-generated from title if not provided)
---
```

#### Required Fields
- `title`: The main heading of your blog post
- `author`: Author's name (e.g., "Mart Tamm", "Pilvio Team")
- `publishedAt`: Date in Estonian format (e.g., "15. november 2024")
- `category`: Main category for the post
- `tags`: Array of relevant tags for discoverability
- `featured`: Boolean indicating if this is the featured article (only one should be `true`)

#### Optional Fields
- `excerpt`: Custom description (if not provided, first 200 characters are used)
- `readingTime`: Custom reading time (if not provided, calculated automatically)
- `imageUrl`: URL to featured image for the post
- `slug`: Custom URL slug (if not provided, generated from title)

### Content Guidelines

#### Markdown Format
- Use standard Markdown syntax
- Headings should start from `##` (h2) since the title is h1
- Use code blocks with language specification for syntax highlighting

#### Code Blocks
Syntax highlighting is supported for many languages. Use triple backticks with language specification:

```yaml
```javascript
const example = () => {
  console.log("Hello, world!");
};
```

Supported languages include:
- `javascript`, `typescript`, `python`, `yaml`, `bash`, `json`
- `kubernetes`, `terraform`, `dockerfile`, `sql`
- And many more via highlight.js

#### Images
- Use absolute URLs for images
- Optimize images for web (recommended: 800x400px for featured images)
- Include meaningful alt text

#### Estonian Content
- Write content in Estonian
- Use Estonian date format: "15. november 2024"
- Technical terms can remain in English when appropriate

## 🏷️ Categories and Tags

### Common Categories
- `Kubernetes`
- `AWS`
- `Terraform`
- `DevOps`
- `Turvalisus` (Security)
- `Arhitektuur` (Architecture)

### Popular Tags
- `kubernetes`, `aws`, `terraform`, `devops`
- `docker`, `python`, `javascript`
- `microservices`, `security`, `ci-cd`
- `algajatele` (for beginners)

## 🚀 Publishing Workflow

### Adding a New Blog Post

1. **Create the file**: Add a new `.md` file in the `posts/` directory
2. **Add frontmatter**: Include all required frontmatter fields
3. **Write content**: Use Markdown format with proper headings and code blocks
4. **Set featured**: If this should be the featured article, set `featured: true` and ensure all other posts have `featured: false`
5. **Commit and push**: Changes are automatically deployed

### Content Review Process

1. Create content in a new branch
2. Submit a pull request
3. Review for:
   - Proper frontmatter format
   - Content quality and accuracy
   - Estonian language correctness
   - Technical accuracy
4. Merge to main branch for automatic deployment

## 🔧 Technical Details

### GitHub API Integration
- Content is fetched via GitHub Contents API
- Repository: `pilvio/blog-content` (or as configured)
- Path: `posts/` directory
- Cache: 5-minute cache for performance

### Environment Variables
The blog application uses these environment variables:
```
REACT_APP_CONTENT_REPO=pilvio/blog-content
```

### Performance
- Content is cached for 5 minutes
- Automatic fallback to cached content if GitHub API is unavailable
- Reading time calculation: ~200 words per minute

### Error Handling
- Invalid frontmatter is logged but doesn't break the build
- Missing required fields use sensible defaults
- Failed file processing is logged and skipped

## 📋 Example Blog Post

```markdown
---
title: "Kubernetes alustamine: Praktilised nõuanded algajatele"
author: "Mart Tamm"
publishedAt: "15. november 2024"
category: "Kubernetes"
tags: ["kubernetes", "devops", "konteinerid", "algajatele"]
featured: true
excerpt: "Õpi Kubernetes põhialused ja kuidas luua oma esimest klastrit."
readingTime: "8 min"
imageUrl: "https://images.unsplash.com/photo-1518770660439-4636190af475?w=800&h=400&fit=crop"
---

## Sissejuhatus

Kubernetes on muutunud tänapäeva konteinerite orkestreerimise standardiks...

## Kubernetes põhimõisted

### Podid
Kubernetes pod on väikseim deployable unit...

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.21
```

## Kokkuvõte

Selles artiklis käsitlesime...
```

## 🛠️ Development Setup

If you need to test content locally:

1. Clone this repository
2. Set up the main blog application
3. Point the `REACT_APP_CONTENT_REPO` environment variable to your fork
4. Content will be automatically fetched and displayed

## 📞 Support

For questions about content formatting or technical issues:
- Create an issue in this repository
- Contact the Pilvio development team
- Check the main blog application logs for debugging

## 🔄 Content Migration

When migrating from other platforms:
- Convert HTML to Markdown
- Extract metadata to frontmatter
- Update image URLs to absolute paths
- Ensure Estonian date format compliance
- Validate all required frontmatter fields
