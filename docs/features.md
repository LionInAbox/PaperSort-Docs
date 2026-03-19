# Welcome to Your Docs

This page demonstrates every feature in your `mkdocs.yml` template.

---

## Tables

| Feature          | What It Does              | Enabled By       |
|------------------|---------------------------|------------------|
| Code copy button | Copies code with a click  | content.code.copy|
| Dark mode toggle | Switches color scheme     | theme.palette    |
| Back to top      | Scrolls you up            | navigation.top   |

---

## Code Blocks with Syntax Highlighting

### Python

```python
def greet(name: str) -> str:
    """Return a greeting message."""
    return f"Hello, {name}!"

print(greet("World"))
```

### JavaScript

```javascript
function greet(name) {
  return `Hello, ${name}!`;
}

console.log(greet("World"));
```

---

## Admonitions (Callout Boxes)

!!! note "This is a Note"
    Notes are useful for extra context that supports the main text.

!!! warning "This is a Warning"
    Warnings draw attention to something that could cause problems.

!!! tip "This is a Tip"
    Tips highlight helpful shortcuts or best practices.

!!! danger "This is a Danger Box"
    Danger boxes flag things that can break something or cause data loss.

---

## Collapsible Details

??? info "Click me to expand"
    This content is hidden by default. The user has to click the header to reveal it. This is powered by `pymdownx.details`.

???+ note "I start expanded (click to collapse)"
    Adding a `+` after `???` means this block starts open. Click the header to collapse it.

---

## Tabbed Content

=== "macOS"

    Open your terminal and run:

    ```bash
    brew install mkdocs
    ```

=== "Windows"

    Open PowerShell and run:

    ```bash
    pip install mkdocs
    ```

=== "Linux"

    Open your terminal and run:

    ```bash
    sudo pip install mkdocs
    ```

---

## Table of Contents Permalink Anchors

### This Heading Has an Anchor Link

Hover over the heading above. You will see a clickable link icon (¶) appear next to it.

### Another Heading to Test

Each heading on the page gets its own anchor. You can click it to copy a direct URL to that section.

---

## Search Highlight Test

Try searching for the word **pineapple** using the search bar at the top of the site. When you click the result, the word pineapple will be highlighted on the page.

Here is the word again: pineapple. And once more: pineapple.

---

## Navigation Top (Back to Top Button)

Scroll all the way down to the bottom of this page, then start scrolling back up slowly. A "Back to top" button will appear at the top of the page.

---

## Filler Content (So the Page Is Long Enough to Scroll)

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo.

Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt.

Neque porro quisquam est, qui dolorem ipsum quia dolor sit amet, consectetur, adipisci velit, sed quia non numquam eius modi tempora incidunt ut labore et dolore magnam aliquam quaerat voluptatem.

Ut enim ad minima veniam, quis nostrum exercitationem ullam corporis suscipit laboriosam, nisi ut aliquid ex ea commodi consequatur.
