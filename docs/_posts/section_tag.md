# HTML `<section>` Tag

The HTML `<section>` element is a semantic HTML5 tag used to group related content. It represents a standalone section of a document, which is typically identified by a heading.

## Purpose and Semantics

*   **Grouping Related Content:** The primary purpose of `<section>` is to group together content that is thematically related. This helps in structuring the document logically.
*   **Semantic Meaning:** Unlike a generic `<div>`, the `<section>` tag conveys semantic meaning to browsers, search engines, and assistive technologies (like screen readers). It indicates that the enclosed content forms a distinct, self-contained part of the document.
*   **Requires a Heading:** According to HTML5 specification best practices, every `<section>` should ideally have a heading element (`<h1>` to `<h6>`) as its first child. This heading provides a clear title or theme for the section's content.

## When to Use `<section>`

Use `<section>` when the content within it can be considered a distinct, self-contained part of the document, and it would make sense to list the content of the section in a document's outline.

**Good Use Cases:**
*   Chapters of a book
*   Tabbed content sections
*   News articles (e.g., a section for the main story, a section for related articles)
*   Contact information section
*   About Us section
*   Product features section
*   Testimonials section

**When NOT to Use `<section>`:**
*   **For purely styling purposes:** If you just need a container for styling or scripting, a `<div>` is more appropriate. The `<section>` tag implies semantic grouping.
*   **For the main content of a page:** The `<main>` element is specifically designed for the dominant content of the `<body>`.
*   **For navigation links:** Use `<nav>`.
*   **For self-contained, reusable content:** Use `<article>` (e.g., a blog post, a forum post, a user-submitted comment).
*   **For side content:** Use `<aside>`.

## Example Usage

```html
<body>

  <header>
    <h1>My Awesome Website</h1>
    <nav>
      <ul>
        <li><a href="#about">About</a></li>
        <li><a href="#services">Services</a></li>
        <li><a href="#contact">Contact</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <section id="about">
      <h2>About Us</h2>
      <p>We are a company dedicated to providing...</p>
      <p>Our mission is to...</p>
    </section>

    <section id="services">
      <h2>Our Services</h2>
      <p>We offer a wide range of services, including:</p>
      <ul>
        <li>Web Development</li>
        <li>Mobile App Development</li>
        <li>UI/UX Design</li>
      </ul>
    </section>

    <section id="contact">
      <h2>Contact Us</h2>
      <p>Feel free to reach out to us via:</p>
      <address>
        Email: <a href="mailto:info@example.com">info@example.com</a><br>
        Phone: (123) 456-7890
      </address>
    </section>
  </main>

  <footer>
    <p>&copy; 2025 My Awesome Website. All rights reserved.</p>
  </footer>

</body>
```

## Relationship with Document Outline

In HTML5, elements like `<section>`, `<article>`, `<nav>`, and `<aside>` contribute to the document outline. Each of these elements, when containing a heading, creates a new section in the outline. While modern browsers and assistive technologies don't always strictly follow the HTML5 outlining algorithm for navigation, using these semantic tags correctly still improves the document's structure and accessibility.

## Styling with `<section>`

You can style `<section>` elements just like any other HTML element using CSS. Since it's a block-level element, it will take up the full width available by default.

```css
section {
  padding: 20px;
  margin-bottom: 30px;
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 8px;
}

section h2 {
  color: #333;
  font-size: 2em;
  margin-bottom: 15px;
}
```

By using `<section>` appropriately, you contribute to a more meaningful, accessible, and maintainable web document structure.
