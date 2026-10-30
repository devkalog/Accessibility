[README (2).md](https://github.com/user-attachments/files/34172553/README.2.md)
# Aboutnet Accessibility

A WordPress plugin that automatically fixes the accessibility errors [WAVE](https://wave.webaim.org/) reports (**Errors** and **Contrast Errors**, WCAG 2.1 AA). It comes with settings that suggest what each option needs for compliance, a page checker in the admin and an on-page editor, similar to the Elementor panel.

- **Version:** 1.8.0
- **Author:** Aboutnet
- **Requires:** WordPress 6.3+, PHP 7.4+
- **Tested up to:** WordPress 7.1, PHP 8.4
- **License:** GPL-2.0-or-later

The admin screens are in Greek. The texts the plugin adds for screen readers come in Greek and English, picked by the language of the page.

> The plugin does not change posts, pages, templates or the theme, and deactivating it brings the site back exactly as it was. The one exception is image alt text that you explicitly save to the Media Library.

---

## Two modes

Chosen under **Προσβασιμότητα → Ρυθμίσεις → Γενικά → Τρόπος λειτουργίας** (Settings → General → Mode).

| | Fast («Γρήγορη») | Permanent («Μόνιμη») |
|---|---|---|
| Where the fixes are made | In the visitor's browser, by `runtime.js` | On the server, in the HTML before it is sent |
| Without JavaScript | Not fixed | Fixed: every checker sees the fixes |
| Colour contrast | Measured live on every page | Measured by the page checker, stored as CSS |
| Visitors load | `runtime.min.js` (31 KB, 11 KB compressed), not delayed by optimisers | Nothing, or the safety net script (deferred, runs when the browser is idle, may be delayed) |
| Server cost | None | About 8 ms for a typical page; none for pages served from the page cache |
| Needs | Nothing | A page checker run on all pages, desktop and mobile, and again when the design changes |

**Safety net** (permanent mode, on by default): the same runtime, loaded with `defer` and run once when the browser is idle after the page has loaded. It covers what the server cannot see: content that other scripts add later (popups, AJAX filters, the WooCommerce block checkout, which React builds in the browser), colours on pages the checker has not measured yet, and the slider pause buttons (they need a script to work). With it off, visitors load no plugin script at all.

The admin bar on the front end has «Η σελίδα χωρίς το script του plugin» (the page without the plugin script) in permanent mode, the same as `?abt-a11y-nojs=1`: what a visitor gets with no plugin JavaScript.

---

## What it fixes

Each fix can be turned on or off under **Προσβασιμότητα → Ρυθμίσεις → Διορθώσεις** (Accessibility → Settings → Fixes).

| Fix | WCAG | What it does |
|---|---|---|
| SVG icons | 1.1.1 | Hides decorative SVGs from screen readers |
| Images | 1.1.1 | Adds alt: empty for decorative images, otherwise from the title, caption or file name |
| Links | 2.4.4 | Names links without text (social icons, image links, banners with a background image) from the destination page, the settings or the URL |
| Buttons | 4.1.2 | Names icon buttons (menu, search, slider arrows, close and others) |
| Forms | 1.3.1, 3.3.2 | Labels for fields, duplicate ids, a second label pointing at the same field |
| Headings and tables | 1.3.1 | Empty headings become plain elements, empty `<th>` cells get a name |
| ARIA | 4.1.2 | Broken `aria-labelledby` / `aria-describedby`, empty `role="menu"` |
| Language | 3.1.1 | Invalid codes such as `el_GR` → `el-GR` |
| Skip link | 2.4.1 | A "skip to content" link that leads nowhere |
| Colour contrast | 1.4.3 | Darkens or lightens only as much as needed, using your palette. Text over a photo keeps its colour and a dark colour goes under the photo |
| Slider pause | 2.2.2 | A pause button on sliders that move on their own (Swiper / Elementor, Slick) |
| Visible focus | 2.4.7 | An outline for keyboard navigation |

WAVE **Alerts** are not covered: they are things for a person to review, not violations in themselves.

---

## Installation

### From a zip

Download `aboutnet-a11y-<version>.zip` from the project's **Releases** on GitLab, then go to **Plugins → Add New → Upload Plugin** in WordPress. If the plugin is already installed, choose **Replace current with uploaded**.

Do not use GitLab's "Source code (zip)": the folder inside it has a different name and WordPress would treat it as a different plugin. The folder must be called `aboutnet-a11y`.

To build a zip from the code:

```bash
git archive --format=zip --prefix=aboutnet-a11y/ -o aboutnet-a11y.zip HEAD
```

### Over SFTP

Upload the plugin's files to `wp-content/plugins/aboutnet-a11y/`.

### First steps

1. Activate the plugin under **Plugins**.
2. **Προσβασιμότητα → Ρυθμίσεις** (Settings): click «Εφαρμογή όλων των προτάσεων» (apply all suggestions), then «Αποθήκευση» (save).
3. **Προσβασιμότητα → Έλεγχος σελίδων** (Page checker): «Έναρξη ελέγχου» (start). Add the colour mappings and link names it suggests.
4. Check a few pages with WAVE itself.
5. The page cache of the common caching plugins is emptied automatically on activation. Clear any server or CDN cache (e.g. Cloudflare) yourself.

---

## Usage

### Settings with suggestions

Every setting shows whether it is compliant, needs attention or is not compliant. When it is not right, it suggests a value, e.g. the closest shade that passes, with its contrast ratio. «Εφαρμογή όλων των προτάσεων» applies all suggestions at once.

### Page checker

Opens the site's pages in the background, with and without the fixes, on desktop and mobile widths. It counts errors and contrast errors with WAVE's rules and suggests colour mappings and link names that can be saved with one click. Each page in the results links to the on-page editor.

In permanent mode, for each page it:

1. loads the page as the server fixes it, with the runtime and without the stored colours, and collects the colour fixes the runtime makes (text colours, the solid colour under photos);
2. stores them as CSS selectors: one rule for a group of elements that got the same fix (e.g. all product titles), header and footer for the whole site, the rest for that page, per screen width;
3. loads the page again with no plugin script at all and counts it. That is the «Με διορθώσεις» (with fixes) number.

The stored colours are listed under Settings → General, and can be deleted there.

### On-page editor

As an administrator, click **Προσβασιμότητα** (Accessibility) in the admin bar on the front end. The number next to it is red when WAVE errors remain and green when none do.

- **Marks, like WAVE's:**
  - red: errors that remain
  - orange: fixed by estimate, worth a look
  - blue: your own changes
  - green: fixed automatically
- **Click on any element** to open a panel with the changes that fit it:
  - **Image:** alt text with suggestions, or decorative. Saved to the Media Library when the image is there.
  - **Link:** a name, for this link or for every link to the same address. "Do not read" for duplicate links.
  - **Button, form field:** a name or label, with suggestions.
  - **Icon:** decorative or named.
  - **Text:** colour with a live preview and contrast check: the closest shade that passes, colours used on the site, or your own.
  - **Text over a photo or video:** WAVE does not see the photo; it measures against the first solid colour underneath (usually white). So the panel shows two live readings for every option:
    - **WAVE:** whether WAVE will report an error.
    - **On the photo:** the real contrast, measured on the photo's pixels or the video's frames, with the overlays on top (e.g. Elementor's), at the lightest spot behind the text (the darkest for dark text). When the browser cannot read the photo (another domain without CORS), it assumes a white photo, the worst case.

    The options that keep your colour:
    - **A dark colour under the photo**, which does not show, when the photo covers the whole box. The plugin also does this on its own.
    - **A backing behind the text**, only for text over a photo: colour, opacity slider, glass effect (blurs the photo behind it, 2–24px), behind this text or behind the whole box of texts around it. It shows from which opacity the text is readable on the photo, with a tick on the slider. WAVE does not measure text on a semi-transparent background, so it reports no error at any opacity below 100%; the reading on the photo is the one that tells whether the text is readable.
- **The panel:**
  - drag it by its top bar anywhere; resize it from the bottom-right corner.
  - drop it at the left or right edge to dock it there at full height; resize the docked panel from its inner edge. ⇆ moves it to the other side.
  - «–» collapses it to its title bar; clicking an element opens it again.
  - it moves to the other side when it covers the element being edited.
  - keyboard: Tab to the handle (the dots left of the title), then the arrow keys; Shift for bigger steps.
  - it remembers its position and size in this browser. On phones it stays at the bottom of the screen.
- **Where a change applies:** here only, on all similar elements, or on the whole site (the default for header and footer).
- **Multilingual sites (WPML, Polylang, TranslatePress):** a change made on a page also applies to the same page in the other languages. Names and alt texts apply only to the language they were written in; colours, backings and hiding apply to all languages.
- All changes are listed, and can be removed, under **Ρυθμίσεις → Αλλαγές από τη σελίδα** (Settings → Changes made on the page).

### For administrators

- `?abt-a11y=off` in the address shows the page without fixes. By default this works for administrators only.
- Export and import settings (JSON) under **Ρυθμίσεις → Εργαλεία** (Settings → Tools).
- **Deleting the plugin** (Settings → General → «Να σβήνεται κάθε ίχνος του plugin», remove every trace). It applies to **Plugins → Delete**, not to deactivation:
  - **ticked:** the settings, the changes made on the page, the stored colour fixes of the permanent mode and what WordPress keeps about the plugin (recently deactivated list, update check) are removed from the database. On multisite, for every site that has it ticked.
  - **not ticked:** the settings stay and come back if the plugin is installed again.
  - either way, WordPress deletes the plugin's files from the server.
  - alt texts saved to the Media Library stay: they are site content. The only thing left in the administrator's browser is the editor panel's position.
- **Page cache:** on activation, deactivation, deletion, when the settings are saved and after a page check, the plugin empties the page cache (WP Rocket, LiteSpeed, W3 Total Cache, WP Super Cache, Autoptimize, SiteGround, WP-Optimize, WP Fastest Cache, Cache Enabler, Breeze, Hummingbird, Nginx Helper, WP Engine). For any other cache, use the `abt_a11y_purge_caches` hook.

---

## Compatibility

Checked for 1.8.0 on local WordPress sites with MySQL (MariaDB 10.11), with automated browser tests (settings, page checker, on-page editor, backings, deletion, permanent mode) and WAVE's rules:

| | Versions | Result |
|---|---|---|
| WordPress | 6.3 (minimum), 6.9, 7.1 | All tests pass, both modes |
| PHP | 7.4, 8.0, 8.1, 8.2, 8.3, 8.4 | All tests pass, no PHP notices or deprecations from the plugin. PHPCompatibility clean for 7.4–8.4; WordPress security sniffs (escaping, nonces, input) clean |
| Themes | Twenty Seventeen to Twenty Twenty-Five, Hello Elementor 3.5 | WAVE errors on the test pages go to 0 with every theme, no JavaScript errors. Permanent mode, 8 pages per theme (home, page, post, category, search, 404), desktop and mobile: 0 errors and 0 contrast errors with no plugin script, and the HTML structure is unchanged apart from the fixes |
| WooCommerce | 11.2 (block cart and checkout) | A full order goes through with the plugin active, in both modes (also with no plugin script). Shop, product, cart and account pages as good in permanent mode without a script as in fast mode; the block checkout, which is built in the browser, needs the safety net |
| Elementor | 4.3.5 | An Elementor page with heading, image, icon, button, text, image carousel, icon box and social icons widgets, Hello Elementor, full-width template: 0 / 0 in both modes, structure unchanged. Also tested earlier on live Elementor sites (slides, background video, entrance animations, WoodMart). The plugin does not run inside the Elementor editor or preview |
| WPML, Polylang, TranslatePress | — | Language detection, language directories and per-language names were tested with simulated setups (the plugins themselves were not available offline). The plugin does not run in the TranslatePress translation editor |

---

## How it works

- `includes/frontend.php` loads the runtime (`assets/runtime.min.js`, or `runtime.js` with `SCRIPT_DEBUG`) in the footer, with the page's settings in `window.abtA11yConfig`. In fast mode it only reads the HTML on the server, to find destination page titles for links without text.
- **Permanent mode** (`includes/static.php`): an output buffer, started at `template_redirect`, receives the finished page. `includes/html.php` reads it in one pass into a light element tree, following the HTML parsing rules that matter here (void elements, implied end tags, raw text such as scripts and styles, SVG/MathML), and the same passes as the runtime run on that tree. Changes are recorded against byte positions; only the tags that change are rewritten and the rest of the page is sent byte for byte as it was. No DOM is re-serialised. Renaming an empty heading only happens where the browser is certain to read the result the same way. Pages that are not HTML, larger than 4 MB or not UTF-8 pass through untouched, as does any page where an error occurs.
- The stored colour fixes are one option per page (`abt_a11y_bk_<md5 of path>`, not autoloaded) and one for the whole site (`abt_a11y_baked_site`), printed as a short inline stylesheet. Text colours use `:is()` and keep the theme's hover and focus colours on links and buttons.
- `runtime.js` runs the fixes on the page and again when content is added (MutationObserver, Elementor popups, AJAX), when animations end and when the window is resized. Text that was hidden when it was checked (entrance animations, tabs) is checked when it appears. When a background video starts or a photo appears, the texts over it are checked again.
- It does not run in page builder editors and previews (Elementor, Beaver Builder, Divi, Oxygen, Bricks, WPBakery, Thrive, Brizy, Breakdance), the Customizer or the TranslatePress translation editor.
- `assets/checker.js` is a clone of WAVE 3.3.3's rules, calibrated on real WAVE results. The page checker and the on-page editor use it.
- The settings are one option, `abt_a11y_settings`. Changes made on the page are stored there as `rules`: selector, kind (`name`, `alt`, `hide`, `color`, `plate`), value, scope (`site` or a page path without the language directory) and, for names and alt texts, the language. `plate` is a backing behind a text or a box: `#1a1a1a@0.65` is that colour at 65% opacity, and `#1a1a1a@0.4/12` adds a 12px blur (glass).
- In fast mode the script asks optimisation plugins (WP Rocket, LiteSpeed, Autoptimize, Cloudflare Rocket Loader) not to delay it. The safety net in permanent mode does not.

### WAVE rules it follows (found by testing)

- An SVG counts as an image without alt unless it has `aria-hidden="true"`, `role="presentation"`, `aria-label` / `aria-labelledby` or a `<title>`. `role="none"` is not enough.
- A link or button has a name from:
  - text (even hidden)
  - an image with alt, or an SVG with title / aria-label
  - `aria-label`, `aria-labelledby` or `title`

  `tabindex="-1"` exempts it.
- A form field has a label from:
  - `aria-label`, `title` or `aria-labelledby`
  - a visible or screen-reader-only `label[for]`
  - a `label` that wraps it

  A placeholder does not count, nor does a label with `display:none`. A `select` without a label is only an alert.
- Contrast:
  - It skips text that (with its parents) is hidden or has font-size 0, and text over a gradient, a filter or a semi-transparent background.
  - The background is the first solid background-color going up. Background images and pseudo-element overlays are ignored; with no background it assumes white.
  - The ratio is truncated to 2 decimals.
  - Large text is ≥24px, or ≥18.66px with weight ≥700.

### Hooks

| Hook | Type | What it does |
|---|---|---|
| `abt_a11y_enabled` | filter | Return `false` to not load on a page |
| `abt_a11y_conflict` | filter | Return the name of code that does the same job, so the two do not both run |
| `abt_a11y_purge_caches` | action | Runs after the plugin asked the known page caches to empty; hook a host or CDN cache here |

---

## Structure

```
aboutnet-a11y.php      Header, constants, loading
uninstall.php          Deleting the plugin: data (if asked) and page cache
readme.txt             WordPress.org-style readme, with the changelog
includes/
  settings.php         Defaults, sanitising, page rules
  cache.php            Emptying the page cache (activation, deactivation, deletion, settings)
  frontend.php         Loading the runtime, its config, link names from the server
  html.php             Permanent mode: the HTML reader and writer, CSS selector matching
  static.php           Permanent mode: the fixes on the server, the stored colour fixes
  admin.php            Settings, page checker, AJAX, import / reset
  editor.php           AJAX for the on-page editor (rules, Media Library)
  inspector.php        Admin bar item and loading the editor on the front end
assets/
  runtime.js           The fixes in the browser (source)
  runtime.min.js       The same, minified: what visitors load
  checker.js           WAVE's rules
  admin.js/.css        Settings and page checker
  inspector.js/.css    On-page editor
```

---

## Releasing a new version

1. Set the new version in three places: `Version` and `ABT_A11Y_VERSION` in `aboutnet-a11y.php`, `Stable tag` in `readme.txt`. Add the changes to the changelog in `readme.txt` and below.
2. If `assets/runtime.js` changed, rebuild the minified file:

   ```bash
   npx terser@5 assets/runtime.js -c -m --comments "/^!/" -o assets/runtime.min.js
   ```

3. Commit, tag and push (example for 1.8.0):

   ```bash
   git add -A
   git commit -m "Aboutnet Accessibility 1.8.0"
   git tag -a v1.8.0 -m "Aboutnet Accessibility 1.8.0"
   git push
   git push origin v1.8.0
   ```

4. Build the plugin zip from the tag:

   ```bash
   git archive --format=zip --prefix=aboutnet-a11y/ -o aboutnet-a11y-1.8.0.zip v1.8.0
   ```

5. On GitLab: **Code → Tags**, click **Create release** on the tag. Title `Aboutnet Accessibility 1.8.0`, the changelog as notes, and the zip attached to the notes.

---

## Changelog

The full changelog is in `readme.txt`.

- **1.8.0:** Two modes: fast (in the browser, as before) and permanent (fixes written into the HTML on the server; colours measured by the page checker and stored as CSS; an optional deferred safety-net script). Minified runtime. Fixes for Elementor full-width skip links and `color(srgb …)` colours. The page cache is emptied when settings are saved.
- **1.7.1:** English documentation and code comments. Multilingual sites: page changes apply to the same page in every language, names and alt texts only in their own language (TranslatePress language directories are now recognised too). Fixes for themes that animate text colours (WoodMart and others: WAVE errors appeared on some page loads) and for fixed text reverting on hover. Not run inside the TranslatePress, Thrive, Brizy and Breakdance editors. Tests and presets removed from the repository.
- **1.6.0:** "Remove every trace of the plugin" option for deletion. The page cache is emptied on activation, deactivation and deletion.
- **1.5.0:** Text over a photo or video: real contrast measured on the photo, a backing behind the text (colour, opacity, glass, box), live WAVE status. Text with entrance animations and background videos that start late are fixed correctly. Theme styles no longer break the editor panel.
- **1.4.0:** New name: Aboutnet Accessibility. The `aboutnet-a11y` folder and the settings stay the same. First release on GitLab.
- **1.3.2:** The editor panel can be moved, resized, docked left or right and collapsed. It moves away from the element being edited.
- **1.3.1:** Text over a photo: a semi-transparent backing behind the text as the first option, instead of a darker colour.
- **1.3.0:** On-page editor with a panel for each kind of element. Marks for the errors that remain. Alt text saved to the Media Library. "Changes made on the page" in the settings.
- **1.2.0:** "What changed on this page", with numbered marks and a list.
- **1.1.1:** Sliders with parallax (WoodMart) and lazy-loaded photos.
- **1.1.0:** Text over a photo that is a separate layer (Elementor Slides, background slideshow).
- **1.0.0:** First version, generalised from the karabinismedical.gr fix.

---

## License

GPL-2.0-or-later. See <https://www.gnu.org/licenses/gpl-2.0.html>.
