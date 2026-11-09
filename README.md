# Allan Hancock College Library — Primo NDE customization

Customization package for the Allan Hancock College Library's **Primo NDE** view, **01CACCL_AHC:NDE**. Changes are developed and tested first in the TEST view ([kellyekh/nde_test](https://github.com/kellyekh/nde_test)), then copied here.

This repository uses Ex Libris's **customization package** approach: plain CSS, JavaScript and HTML in an `assets` folder that is zipped and uploaded to Alma. (It does not use the Angular-based [customModule](https://github.com/ExLibrisGroup/customModule) development environment.)

**This is the production (live) view.** Uploading a new package here changes what patrons see, so test every change in the TEST view first.

**View:** [01CACCL_AHC:NDE](https://caccl-ahc.primo.exlibrisgroup.com/nde/home?vid=01CACCL_AHC:NDE)

## Repository layout

```
01CACCL_AHC-NDE/           The view. Its name must match the view code, because it becomes
│                          the top-level folder in the upload zip.
├── assets/                Everything Primo loads (this is the only part that gets uploaded)
│   ├── css/custom.css         Site-wide styling
│   ├── js/custom.js           Site-wide behavior (book slider, Requests section, etc.)
│   ├── homepage/              Homepage HTML (homepage_en.html), styles and background
│   ├── header-footer/         Footer HTML
│   ├── html/                  Custom HTML snippets
│   ├── icons/                 favicon.ico and custom_icons.svg (icon overrides)
│   └── images/                Library logo, footer logo, loading animation
└── config.json            Add-on configuration (chat queue/snippet, icon colors)

docs/                      Notes and reference material (never uploaded)
├── customization-package-guide.md   Ex Libris's guide to the assets folder structure
├── requests-section-customizations.md
├── digital-versions-signin.md
└── showDirectives.txt
design/                    Source logos and SVGs
```

## Updating the view

1. Edit files in `01CACCL_AHC-NDE/assets/`.
2. Build the upload zip from this folder so the zip's top level is `01CACCL_AHC-NDE/assets/`, without system files:

   ```bash
   zip -r ../01CACCL_AHC-NDE.zip 01CACCL_AHC-NDE/assets -x "*.DS_Store" "*__MACOSX*"
   ```

   (In Claude Code, the `/zip` skill does this and checks the result.)
3. In Alma: **Configuration → Discovery → Display Configuration → Configure Views** → edit **01CACCL_AHC:NDE** → **Manage Customization Package** → upload the zip → **Save**.
4. Check the view, then commit and push:

   ```bash
   git add .
   git commit -m "Describe the change"
   git push
   ```

## Moving changes from TEST to NDE

TEST and NDE share the same customization; only view-specific paths differ. When copying a change over from the TEST repo, check for these:

- Links with `vid=01CACCL_AHC:TEST` (homepage collection links, slider "View all") must say `vid=01CACCL_AHC:NDE`.
- Asset paths such as `/nde/custom/01CACCL_AHC-TEST/assets/...` (the footer logo) must say `01CACCL_AHC-NDE`.

Search for leftovers before zipping: `grep -rn TEST 01CACCL_AHC-NDE/assets`

## Related repositories

- [kellyekh/slider](https://github.com/kellyekh/slider) — `books.md`, the book list for the homepage's "Check out something new" slider. `custom.js` fetches it at page load, so editing that file updates the slider without re-uploading the package.
- [kellyekh/homepage-content](https://github.com/kellyekh/homepage-content) — `featured-collection.md`, content for the homepage's Featured Collection box.

## Development notes

### NDE is a single-page app
Homepage content can mount, unmount and remount as people navigate without a full page reload, so code can't rely on a single load event. `custom.js` uses a `MutationObserver` to set up (and tear down) the book slider whenever its markup appears or disappears.

### Loading content from GitHub
Raw GitHub URLs (`raw.githubusercontent.com`) are CORS-enabled for public repositories, so `custom.js` can fetch Markdown from them directly with `fetch()`, with no proxy. If a source repo's branch name or file path changes, update the URL in `custom.js`.

### Known quirks we've accepted
- `assets/icons/custom_icons.svg` is empty, so no default icons are overridden.
- The footer file is named `footer.html` rather than Ex Libris's `footer_en.html` convention.

### Labels vs. styling
Text such as `nui.request.signin_additional` is edited as a label in Alma; this repository only controls how it looks. See `docs/requests-section-customizations.md` for the Requests section.
