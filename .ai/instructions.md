# Hugo TYPO3 Lexikon - AI Instructions

This file contains the configuration specifications and workflows for the typo3lexikon repository.

## Infrastructure
* **Hugo Version:** 0.160.1 (Extended)
* **Hugo Theme:** hugo-theme-relearn (v9.0.3)

## Branding & Logo Setup
* **SVG Logo Path:** `assets/images/typo3-logo.svg`
* **Configuration (`hugo.toml`):** Logos are configured in `themeVariant` params using the `?inlinecontent` query parameter (e.g. `/images/typo3-logo.svg?inlinecontent`).
* **Sizing/Coloring:** The theme's native `auto-logo.html` handles the logo rendering. The `inlinecontent` parameter allows the theme to automatically colorize the text portions of the SVG depending on the active theme variant (light/dark mode).
* **Trademark Disclaimer:** Located in `layouts/partials/menu-footer.html`. Do not override `logo.html` for this purpose.

## Deployment & CI/CD
* **Workflow:** Push to `main` triggers `.github/workflows/hugo.yml` to compile and deploy.
* **Deployment Method:** Deploys via SFTP mirror (`lftp`).
* **Constraint:** Do not switch to `rsync` (the target host does not support it).
* **Runner Environment:** Runs on `ubuntu-latest` with checkout/artifact actions supporting Node 24.

## Git Rules
* **Ignore Files:** `/.hugo_build.lock` (untracked) and `/resources/_gen/` are excluded in `.gitignore`.

## Project-wide Formatting & File Rules
These rules apply to all files in the repository (supported by `.editorconfig`):
*   **Trailing Whitespace:** All trailing whitespace at the end of lines must be removed.
*   **Line Endings:** Every file must end with a single newline (LF) at the end of the file.
*   **Consecutive Empty Lines:** A maximum of one consecutive empty line is allowed in all file types.

## Version Badges Syntax (TYPO3 Versions)
Use the following Hugo shortcodes to represent TYPO3 version support status. Note that the version numbers below (e.g., 11.5, 12.4, 13.0) are examples and must be updated on each page to reflect the actual TYPO3 version history for that feature:

*   **Supported Since (Green):**
    ```html
    {{% badge style="green" icon="angle-double-up" %}}TYPO3 11.5{{% /badge %}}
    ```
*   **Deprecated Since (Orange):**
    ```html
    {{% badge style="orange" icon="angle-double-up" %}}TYPO3 12.4{{% /badge %}}
    ```
*   **Removed Since (Red):**
    ```html
    {{% badge style="red" icon="skull-crossbones" %}}TYPO3 13.0{{% /badge %}}
    ```

## Markdown-specific Formatting Rules
*   **Headers:** Always leave exactly one empty line after each heading/header (`#`, `##`, `###`, etc.) in all Markdown (`*.md`) files. This ensures clean Hugo rendering and consistent raw source file layout.
*   **Code Blocks:** Always leave exactly one empty line before and after each code block (e.g., ` ```shell `) to ensure proper formatting and parsing by Markdown engines.

## Local Development Commands
*   **Start Local Server:**
    ```shell
    hugo server
    ```
*   **Compile Production Build Locally:**
    ```shell
    hugo --gc --cleanDestinationDir --minify
    ```

## Performance Optimizations

*   **Collapsible Menu (`collapsibleMenu`):** Always leave `collapsibleMenu` disabled (`false` or unset) globally and in front matter. Enabling it causes exponential build time slowdowns (e.g., up to 30+ minutes for large sites).
*   **External Link Checking (`urlExternalCheck`):** Keep `urlExternalCheck = false` in `hugo.toml` to avoid network requests during build.
*   **Asset Minification (`minify`):** Keep `minify` parameter unset in `hugo.toml` under `[params]`. The theme automatically handles minification, serving unminified files locally for fast refresh times, and minifying in production when built with the `hugo --minify` flag.
*   **Print Output Format:** Keep `print` output format disabled for pages/sections/home (only build standard HTML and RSS) to prevent Hugo from building every page twice.
*   **Hugo Caching:** Use Hugo version `0.149.0` or higher to benefit from the theme's built-in template/page-tree caching optimizations.
