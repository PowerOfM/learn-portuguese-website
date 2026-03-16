# Plan: Manuela's Portuguese Tutor Website

## Context

Build a static website from scratch for Manuela, a European Portuguese tutor based in Vancouver, BC. She is a native speaker with 20+ years of teaching experience offering individual and group tutoring at A1–B2 levels. The site must be warm, professional, encouraging, and trustworthy. Technical requirements: Eleventy (11ty) + Nunjucks templating + vanilla CSS, no JS frameworks.

The repository (`/home/user/learn-portuguese-website`) is empty. We build everything from the initial commit.

---

## 1. Website Content Outline

### Pages
| Page | URL | Purpose |
|------|-----|---------|
| Home | `/` | Hero intro, quick trust signals, CTA to contact |
| About | `/about/` | Manuela's story, qualifications, teaching philosophy |
| Services | `/services/` | All tutoring offerings with descriptions |
| Contact | `/contact/` | Contact form + booking invitation |

### Navigation (top bar)
`Manuela Portuguese` (logo/name) — Home · About · Services · Contact · [Book a Free Call] (CTA button)

### Page Sections Detail

**Home (`index.njk`)**
1. **Hero** — Headline: "Learn Portuguese with Confidence", sub-headline about Manuela, CTA button "Book a Free Consultation"
2. **Why Learn Portuguese** — Short motivational blurb (travel, culture, career, family connection)
3. **Why Manuela** — 3-column trust icons: Native Speaker · 20+ Years Experience · Certified Teacher
4. **Services Snapshot** — 3 cards (Individual, Group Beginner, Group Intermediate/Advanced) with "Learn More" links
5. **Testimonials** — 2–3 student quotes (placeholder for real testimonials)
6. **CTA Banner** — "Ready to start your Portuguese journey?" + contact button

**About (`about/index.njk`)**
1. **Intro with photo slot** — `<img src="/assets/images/manuela.jpg" alt="Manuela">` placeholder (image file not included, template has a `TODO` comment). Manuela's story: born in Portugal, now in Vancouver
2. **Teaching Philosophy** — Warm, immersive, student-centred approach
3. **Qualifications** — Career teacher, 20+ years with children and adults, native European Portuguese
4. **Fun cultural touches** — Brief mention of Portuguese culture (azulejos, fado, cuisine) to set warm tone
5. **CTA** — "Let's work together"

**Services (`services/index.njk`)**
1. **Page intro** — Brief overview statement
2. **Individual Tutoring card** — Flexible schedule, personalised curriculum, any level
3. **Group: A1 Beginners card** — Complete beginners, small group, structured curriculum
4. **Group: A2 Intermediate card** — Some exposure to Portuguese, building confidence
5. **Group: B1/B2 Advanced card** — Conversation fluency, grammar refinement, cultural immersion
6. **CEFR Level explainer** — Brief callout box explaining what A1/A2/B1/B2 means
7. **CTA** — "Not sure which level is right for you? Get in touch"

**Contact (`contact/index.njk`)**
1. **Intro text** — Friendly invite to reach out
2. **Email CTA** — Prominent `mailto:` link styled as a button (placeholder email with TODO comment)
3. **Response time note** — e.g. "I typically reply within 24 hours"

---

## 2. Eleventy Project Structure

```
learn-portuguese-website/
├── .eleventy.js                  # Eleventy config
├── .gitignore
├── package.json                  # npm config with @11ty/eleventy
├── src/
│   ├── _data/
│   │   ├── site.json             # Global site metadata (title, description, author)
│   │   └── services.json         # Service offering data
│   ├── _includes/
│   │   ├── layouts/
│   │   │   └── base.njk          # Base HTML shell (head, header, footer)
│   │   ├── partials/
│   │   │   ├── header.njk        # Site nav
│   │   │   ├── footer.njk        # Footer links + copyright
│   │   │   ├── service-card.njk  # Reusable service card macro
│   │   │   └── testimonial.njk   # Reusable testimonial block
│   ├── assets/
│   │   ├── css/
│   │   │   ├── main.css          # Imports all partials via @import
│   │   │   ├── _variables.css    # Custom properties (colors, type, spacing)
│   │   │   ├── _reset.css        # Minimal reset
│   │   │   ├── _base.css         # Body, typography defaults
│   │   │   ├── _layout.css       # Page layout, grid, containers
│   │   │   ├── _header.css       # Nav styles
│   │   │   ├── _footer.css       # Footer styles
│   │   │   ├── _hero.css         # Hero section
│   │   │   ├── _cards.css        # Service and content cards
│   │   │   ├── _buttons.css      # Button variants
│   │   │   ├── _forms.css        # Contact form
│   │   │   └── _utilities.css    # Helper classes
│   │   └── images/
│   │       └── placeholder/      # Placeholder image references
│   ├── index.njk                 # Home page
│   ├── about/
│   │   └── index.njk             # About page
│   ├── services/
│   │   └── index.njk             # Services page
│   └── contact/
│       └── index.njk             # Contact page
└── _site/                        # Eleventy build output (gitignored)
```

### `package.json` dependencies
```json
{
  "devDependencies": {
    "@11ty/eleventy": "^3.0.0"
  },
  "scripts": {
    "start": "eleventy --serve",
    "build": "eleventy"
  }
}
```

### `.eleventy.js` configuration
```js
module.exports = function(eleventyConfig) {
  eleventyConfig.addPassthroughCopy("src/assets");
  return {
    dir: {
      input: "src",
      output: "_site",
      includes: "_includes",
      data: "_data"
    },
    templateFormats: ["njk", "html"],
    htmlTemplateEngine: "njk"
  };
};
```

---

## 3. Nunjucks Template Plan

### `base.njk` (master layout)
- `<!DOCTYPE html>` shell with `<head>` (meta, canonical, CSS link, Google Fonts)
- `{% include "partials/header.njk" %}` — site nav
- `<main>{% block content %}{% endblock %}</main>`
- `{% include "partials/footer.njk" %}` — footer
- All pages extend this: `{% extends "layouts/base.njk" %}`

### `partials/header.njk`
- `<header>` with logo/name link + `<nav>` with page links + CTA button
- Active page highlight using `page.url` variable

### `partials/footer.njk`
- Copyright, nav links, and a tagline

### `partials/service-card.njk`
- Accepts: `title`, `level`, `description`, `icon`, `link`
- Used on both the home page snapshot and full services page
- Called with Nunjucks `{% include %}` or as a macro with `{% from %}` / `{% call %}`

### Page templates
Each page:
1. Extends `layouts/base.njk`
2. Sets `{% block title %}Page Name{% endblock %}`
3. Defines `{% block content %}` with semantic HTML sections

### `_data/services.json` pattern
Service data is stored in JSON and looped over in templates:
```json
[
  { "id": "individual", "title": "Individual Tutoring", "level": "All Levels", "description": "..." },
  { "id": "a1", "title": "Group: Total Beginners", "level": "A1", "description": "..." },
  ...
]
```

---

## 4. CSS Architecture

### Color Palette (CSS custom properties in `_variables.css`)
| Variable | Value | Use |
|----------|-------|-----|
| `--color-primary` | `#C75B3A` | Terracotta — CTAs, accents, hero highlights |
| `--color-secondary` | `#2A5F8F` | Azulejo blue — headers, links, nav |
| `--color-bg` | `#FDF6ED` | Warm cream — page background |
| `--color-surface` | `#FFFFFF` | Card/form backgrounds |
| `--color-text` | `#2C2C2C` | Body text |
| `--color-text-muted` | `#6B6B6B` | Secondary text |
| `--color-border` | `#E8DDD0` | Subtle borders |

### Typography
- **Headings**: `'Playfair Display', Georgia, serif` — elegant, warm (Google Fonts)
- **Body**: `'Lato', system-ui, sans-serif` — clean, readable (Google Fonts)
- Base size: `16px`, fluid type with `clamp()` for headings

### Responsive Strategy
- Mobile-first, single breakpoint at `768px` for larger layouts
- CSS Grid for page layouts; Flexbox for component layouts
- Container max-width: `1100px`, centered with `margin: 0 auto`

### Key UI Components
- `.btn` / `.btn--primary` / `.btn--outline` — button variants
- `.card` — service cards with subtle shadow and border-radius
- `.hero` — full-width hero with background gradient/pattern
- `.section` — standard section padding wrapper
- `.trust-icons` — 3-column flex grid for credential highlights
- `.testimonial` — quote card with left border accent

---

## 5. Content & Form Strategy

### Contact Page
**No form for now** — display Manuela's email address prominently with a friendly invitation to reach out. A `<a href="mailto:...">` link styled as a CTA button. Placeholder email used in template with a clear `TODO` comment so it can be replaced easily.

### SEO Metadata (in `site.json`)
- `description`: "Learn European Portuguese in Vancouver with Manuela — a native speaker and experienced teacher offering individual and group lessons at all levels."
- Open Graph tags in `base.njk`
- Each page sets its own `title` and `description` via front matter

---

## Implementation Steps

1. **Project bootstrap** — `package.json`, `.gitignore`, `.eleventy.js`
2. **CSS variables & reset** — establish design tokens first
3. **Base layout** — `base.njk`, `header.njk`, `footer.njk`
4. **Home page** — hero, trust icons, services snapshot, testimonials, CTA
5. **About page** — bio, philosophy, qualifications
6. **Services page** — full service cards with CEFR explainer
7. **Contact page** — mailto email link
8. **CSS polish** — responsive passes, hover states, accessibility
9. **Data files** — `site.json`, `services.json`
10. **Commit & push** to `claude/portuguese-tutor-website-YlYqK`

---

## Verification

- Run `npm start` → Eleventy dev server at `http://localhost:8080`
- Visit all 4 pages and verify layout, navigation, and responsive behaviour
- Check mobile view at 375px width
- Validate HTML with W3C validator
- Confirm `_site/` output is gitignored
