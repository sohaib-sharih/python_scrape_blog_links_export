# Smythson Blog Scraper

Extracts blog post titles and links from the [Smythson Stories](https://www.smythson.com/uk/smythson-stories.html) page and saves them to an Excel file.

## How it works

1. Opens the page in Chrome using **seleniumbase UC mode** (undetected Chrome that bypasses Cloudflare bot detection).
2. **Waits for you** to manually complete any Cloudflare / human verification if prompted.
3. Locates the `<main>` content area of the page and extracts only blog post tiles from within it.
4. Saves the results to `smythson_blog_posts.xlsx`.

## Why this approach

The Smythson website is protected by **Cloudflare**, which actively blocks automated browsers (Playwright, standard Selenium, etc.) and HTTP-only tools like `cloudscraper` or `curl`. After trialling multiple solutions:

- **Playwright** — Blocked by Cloudflare even with stealth patches and system Chrome channel
- **undetected-chromedriver** — Blocked due to version mismatches and detection
- **cloudscraper / requests** — Returned HTTP 403 (Cloudflare challenge page)
- **seleniumbase UC mode** — ✓ Successfully opens the page and allows manual verification to complete

## How header/footer content is excluded

The script does **not** scrape blindly from the entire page. Instead it:

1. Finds the `<main>` element (`main[role="main"].l-body-page_main`) — this is the semantic HTML tag that contains only the page's core content, excluding headers, navigation menus, and footers by definition.
2. Within `<main>`, locates story tiles by their unique CSS class `.jour-land`.
3. Extracts the title from `a.jorn-blog-name` and the URL from its `href` attribute inside each tile.
4. Falls back to scanning all `<a>` tags within `<main>` only if no `.jour-land` tiles are found — still keeping the search boundary inside the main content area.

This structural approach ensures navigation links, customer service links, privacy policy links, and other non-blog elements are never picked up.

## Requirements

- Python 3.8+
- [Google Chrome](https://www.google.com/chrome/) installed on your system

## Setup

```bash
pip install -r requirements.txt
```

## Usage

```bash
python main.py
```

Chrome will open automatically. If a Cloudflare / CAPTCHA page appears:

1. Wait for the "Verify you are human" checkbox to load
2. Click it
3. Wait for the actual blog listing page to load
4. Press **Enter** in the terminal

The script will scrape the blog posts and generate the Excel file.

## Output

`smythson_blog_posts.xlsx` — an Excel file with two columns:

| Blog Title | Blog Link |
|------------|-----------|
| ...        | ...       |
