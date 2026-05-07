---
name: alan-shopify-develop-skill
description: Build and modify Shopify theme sections following Alan's conventions for Liquid section markup, Tailwind-first styling, schema-driven CSS isolation, section/block settings, section JavaScript, and Figma MCP design implementation. Use when developing, editing, reviewing, or debugging Shopify theme section files, especially `.liquid` section files with schema, Tailwind CSS, Liquid settings, blocks, or Figma-based visual requirements.
---

# Alan Shopify Develop Skill

## Core Workflow

Use this skill together with the standard Shopify Liquid theme skills when editing theme files. Follow the existing theme structure first, then apply Alan's section conventions below.

1. Inspect nearby sections/snippets before editing so naming, schema shape, translation keys, and theme patterns stay consistent.
2. Use Figma MCP to obtain design context when a design is referenced or when visual details are required. Translate the design into Tailwind classes where possible.
3. Keep the section self-contained: root markup, optional `{% stylesheet %}`, optional `{% javascript %}`, and `{% schema %}` should live in the section file unless the existing theme has a clear shared abstraction.
4. Prefer direct, readable Liquid and HTML over new abstractions unless the codebase already uses them.
5. Verify schema JSON, Liquid syntax, responsive spacing, and multi-section isolation before finishing.

## Markup

- Use a `<section>` element for the section root.
- Set the root section id to `id="{{ section.id }}"`.
- Use block tags based on semantics and layout needs; do not force every block into the same tag.
- Keep section/block IDs and data attributes stable enough for scoped CSS and JavaScript.
- Prefer semantic HTML and accessible controls/labels when the section contains navigation, forms, accordions, sliders, buttons, or media.

Example root:

```liquid
<section id="{{ section.id }}" class="...">
  ...
</section>
```

## Styling

- Treat Tailwind CSS as the primary styling method. Write styles directly as Tailwind classes on HTML tags by default.
- Only use custom CSS when the style is driven by schema settings, cannot be expressed cleanly with Tailwind, or matches an existing required theme pattern.
- Do not move static visual styles into `{% stylesheet %}` when Tailwind classes can express them.
- The build system compiles Tailwind classes into the final CSS automatically.
- Use Tailwind's `max-md:` breakpoint for mobile-specific styles.
- Use `{% stylesheet %}` / `{% endstylesheet %}` near the top of the section for CSS that must be generated from schema settings or cannot reasonably be expressed with Tailwind.
- Scope any CSS that depends on `section.settings` or `block.settings` with `#{{ section.id }}` so multiple instances of the same section do not affect each other.
- Use `padding-inline: calc((100% - 1200px) / 2);` for the standard page gutter/content width pattern. Treat `1200px` as the default content width.
- Keep static styling in Tailwind unless the existing codebase pattern strongly suggests otherwise.
- Do not create broad global selectors from a section.
- Use Figma MCP design context for exact spacing, color, typography, layout, and responsive behavior when implementing from a design.

Use this pattern for schema-driven spacing:

```liquid
{% stylesheet %}
  #{{ section.id }} {
    padding-top: {{ section.settings.padding_top }}px;
    padding-bottom: {{ section.settings.padding_bottom }}px;
    padding-inline: calc((100% - 1200px) / 2);
  }

  @media (max-width: 768px) {
    #{{ section.id }} {
      padding-top: {{ section.settings.padding_top_mobile }}px;
      padding-bottom: {{ section.settings.padding_bottom_mobile }}px;
    }
  }
{% endstylesheet %}
```

## JavaScript

- Put section script logic above `{% schema %}`.
- Prefer wrapping section JavaScript in `{% javascript %}` / `{% endjavascript %}`.
- Wrap runtime logic in `DOMContentLoaded` so the section initializes after DOM parsing.
- When the user asks to use a third-party library, assume it is available globally unless they explicitly say it is not installed or not prepared. Use the global directly; do not add extra availability guards just to be defensive.
- Scope DOM queries to the current section when possible so multiple section instances work independently.
- Avoid introducing bundled imports or dependency installation for section JavaScript unless the user explicitly requests it.

Example:

```liquid
{% javascript %}
  document.addEventListener('DOMContentLoaded', () => {
    document.querySelectorAll('section[id]').forEach((sectionEl) => {
      // Initialize scoped behavior here.
    });
  });
{% endjavascript %}
```

## Schema

- Keep `{% schema %}` valid JSON.
- Add settings only when they are needed by the requested UI or by Alan's reusable spacing conventions.
- For configurable spacing, prefer desktop and mobile controls:
  - `padding_top`
  - `padding_bottom`
  - `padding_top_mobile`
  - `padding_bottom_mobile`
- Use clear section and block setting IDs that match their purpose.
- When schema settings drive CSS, ensure the CSS is scoped with `#{{ section.id }}`.

## Figma Implementation

- If the user provides a Figma URL or asks to match a design, use Figma MCP before implementing.
- Extract visual intent, spacing, typography, imagery, responsive layout, and component hierarchy from the design.
- Implement with Tailwind classes first; use scoped stylesheet blocks only for schema-driven values or edge cases Tailwind cannot express cleanly.
- If design tokens or reusable components exist in Figma/code, prefer matching them over inventing new values.

## Review Checklist

Before finishing a Shopify section task:

- Root element is `<section id="{{ section.id }}">`.
- Tailwind is the default styling layer and is used directly on tags wherever possible.
- Custom CSS is limited to schema-driven values, Tailwind edge cases, or required existing theme patterns.
- Standard content width uses `padding-inline: calc((100% - 1200px) / 2);` with `1200px` as the content width.
- Mobile Tailwind styling uses `max-md:` as the breakpoint.
- Schema-driven CSS is inside `{% stylesheet %}` and scoped with `#{{ section.id }}`.
- JavaScript is above schema, inside `{% javascript %}`, and wrapped in `DOMContentLoaded`.
- Third-party globals requested by the user are used directly unless explicitly unavailable.
- Multiple instances of the same section can coexist without CSS or JavaScript conflicts.
- Schema JSON is valid and setting IDs match Liquid references.
