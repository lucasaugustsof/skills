---
name: "semantic-html"
description: "Reviews, rewrites and explains HTML5 semantics (tags, landmarks, headings, forms, ARIA). Use whenever the user pastes HTML or asks for a markup review, the right tags, accessibility or fixing 'divitis'."
---

# Semantic HTML

Helps review HTML, return a corrected version and explain the reasoning behind each choice. The focus is always **plain HTML5**, framework-independent: if the code comes as JSX or another template, analyze only the resulting markup and answer in HTML.

Reply in the language the user writes in.

## Why semantics matters (use this to explain)

The browser turns HTML into the DOM and, from it, builds the **accessibility tree**. Every tag has an implicit role (`<button>` → button, `<nav>` → navigation, `<h2>` → heading level 2). Screen readers, search engines, reader mode and extensions read that tree, not the CSS. A `<div>` styled as a button looks like a button, but to the tree it is just a generic block: it doesn't receive focus, doesn't respond to Enter/Space and isn't announced as a button.

Anchor explanations in this — what the browser understands and what changes for the people using the page — rather than memorized rules. Prefer concrete examples ("the screen reader will announce...", "Tab will skip this element...") over abstractions.

Useful mental test: **if the CSS disappeared, would the structure still make sense?**

## Workflow

1. **Understand the content before the tags.** What is each block? Navigation, main content, a standalone item, an interactive control? If the intent is ambiguous (e.g. a link that looks like a button), assume the most likely reading and state which one you assumed.
2. **Review using the checklist below.**
3. **Answer in the defined format.**

## Review checklist

**Document**
- `<!DOCTYPE html>`, `<html lang="...">`, `<meta charset="utf-8">`, `<meta name="viewport">`, a descriptive `<title>` (only if the snippet is a full document).

**Landmarks**
- `<header>`, `<nav>`, `<main>` (only one visible per page), `<aside>`, `<footer>`.
- `<section>` groups thematic content and normally has a heading; without a heading it is probably a `<div>`.
- `<article>` is content that would make sense on its own (post, product card, comment).
- Multiple `<nav>` on the page → distinguish them with `aria-label`.

**Headings**
- Hierarchy without skipped levels (h2 → h4 is a problem).
- A heading describes its section; don't use headings to make text big, nor styled text in place of a heading.

**Interaction**
- An action on the page → `<button>` (with an explicit `type` inside forms). Navigation to another URL → `<a href>`.
- `<div onclick>`, `<span onclick>` and `<a>` without `href` are red flags.
- Consider native elements before rebuilding them: `<details>/<summary>`, `<dialog>`.

**Forms**
- Every field has an associated `<label>` (`for`/`id` or wrapping the input). A placeholder doesn't replace a label.
- Radio/checkbox groups in `<fieldset>` + `<legend>`.
- Appropriate `type` (`email`, `tel`, `number`, `date`...) and `autocomplete` where it makes sense.

**Lists and tables**
- A set of items → `<ul>`/`<ol>`; term–definition pairs → `<dl>`.
- `<table>` only for tabular data, with `<caption>`, `<thead>` and `<th scope>`. Never for layout.

**Media**
- `<img>` with `alt` describing its purpose; decorative → `alt=""`.
- Image with a caption → `<figure>` + `<figcaption>`.

**Text**
- Meaningful emphasis: `<strong>`/`<em>`; `<b>`/`<i>` only for stylistic offset without importance.
- `<time datetime>`, `<blockquote>`/`<q>`, `<cite>`, `<abbr>`, `<code>`/`<pre>`, `<address>` where they fit.
- `<br>` only for line breaks that are part of the content (address, poem), not for spacing.

**ARIA**
- First rule: if a native element exists, use it instead of ARIA.
- Flag redundant ARIA (`<button role="button">`, `<nav role="navigation">`) and ARIA that contradicts the tag.

**Don't over-correct.** `<div>` and `<span>` are correct when a block exists only for layout or styling. Swapping every `<div>` for a `<section>` makes the accessibility tree worse.

## Response format (mixed tone: direct fix + short why)

### Diagnosis
List of problems grouped by impact:
- **Breaks accessibility/usability** — something that prevents someone from using the page (button without focus, field without label, image without alt).
- **Imprecise semantics** — it works, but communicates the wrong structure (skipped heading, `section` without a title).
- **Detail** — fine-grained improvement.

Each item: what's wrong → what to use. One line.

### Corrected code
The full rewritten HTML, with short comments only on the changed parts.

### Why
2 to 5 short explanations, one per important decision, tied to what the browser/accessibility tree does. If a concept is central (e.g. implicit role, landmark), explain it once with a concrete example.

If the code is already good, say so clearly and point out only what is really worth adjusting.

## Example

Input:
```html
<div class="header">
  <div class="logo">Store</div>
  <div class="menu"><a>Home</a><a>Products</a></div>
</div>
<div class="title">Deals</div>
<div onclick="buy()">Buy</div>
```

Output (abridged):

**Diagnosis**
- Breaks usability: `<div onclick>` → `<button type="button">`; `<a>` without `href` → links with `href`.
- Imprecise semantics: `.header` → `<header>`; `.menu` → `<nav>` with `<ul>`; `.title` → `<h1>`; missing `<main>`.

**Corrected code**
```html
<header>
  <p class="logo">Store</p>
  <nav aria-label="Main">
    <ul>
      <li><a href="/">Home</a></li>
      <li><a href="/products">Products</a></li>
    </ul>
  </nav>
</header>
<main>
  <h1>Deals</h1>
  <button type="button" onclick="buy()">Buy</button>
</main>
```

**Why**
- The `<div>` isn't in the Tab order and doesn't react to Enter; `<button>` comes with focus and keyboard support and is announced as "button".
- An `<a>` without `href` isn't a link to the browser: it doesn't get focus and doesn't show up in the screen reader's links list.
- `<nav>` and `<main>` become landmarks: screen reader users can jump straight to them.

## Sources
When in doubt about a rule, confirm it on MDN or in the HTML Living Standard (if a documentation tool is available, use it) and cite the link.