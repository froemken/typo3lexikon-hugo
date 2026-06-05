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

## Version Badges Syntax (TYPO3 Versions)
Use the following Hugo shortcodes to represent TYPO3 version support status:

*   **Supported Since (Green):**
    ```html
    {{% badge style="green" icon="angle-double-up" %}}TYPO3 12.4{{% /badge %}}
    ```
*   **Deprecated Since (Orange):**
    ```html
    {{% badge style="orange" icon="angle-double-up" %}}TYPO3 12.4{{% /badge %}}
    ```
*   **Removed Since (Red):**
    ```html
    {{% badge style="red" icon="skull-crossbones" %}}TYPO3 13.0{{% /badge %}}
    ```

