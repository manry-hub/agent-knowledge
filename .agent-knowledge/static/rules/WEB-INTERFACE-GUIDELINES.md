# Web Interface Guidelines

Guidelines and rules for building, auditing, and reviewing web user interfaces, adopted from Vercel's Web Interface Guidelines (`web-design-guidelines`).

Apply these rules when implementing, refactoring, or auditing UI components, layouts, forms, and client interactions. Pair this reference with `.agent-knowledge/static/rules/antislop/UI.md` for visual and styling standards, and `.agent-knowledge/static/rules/CODING-STANDARDS.md` for general code quality.

---

## 1. Accessibility

- **Icon-only buttons**: Every button without visible text must have an explicit `aria-label` describing the action.
- **Form controls**: Every input, select, and textarea must be associated with a `<label>` (via `htmlFor` or by nesting) or provide an `aria-label`.
- **Keyboard interaction**: All interactive elements must support keyboard navigation and execution (`onKeyDown`/`onKeyUp`, Enter, Space, Escape where applicable).
- **Semantic HTML**: Use `<button>` for actions and `<a>`/`<Link>` for navigation. Never attach click handlers to `<div>`, `<span>`, or `<p>` without semantic elements.
- **Image descriptions**: All `<img>` tags require an `alt` attribute. Use descriptive text for informational images and `alt=""` for purely decorative images.
- **Decorative icons**: Icons that accompany text or serve purely decorative roles must include `aria-hidden="true"`.
- **Async updates**: Dynamic notifications, validation banners, and toasts must announce to assistive technology using `aria-live="polite"` (or `aria-live="assertive"` for critical alerts).
- **Semantic hierarchy**: Structure documents with logical heading levels (`<h1>` through `<h6>`). Provide a skip-to-content link for main page layouts.
- **Heading anchors**: Include `scroll-margin-top` on heading anchors and section targets to avoid clipping beneath sticky headers.
- **Media accessibility**: Meaningful audio/video requires captions, transcripts, or text alternatives. Media controls must be keyboard-accessible; decorative media must be hidden from assistive technology.

---

## 2. Focus States

- **Visible focus indicator**: All interactive controls must provide a clear, visible focus indicator (e.g., `focus-visible:ring-2`, `focus-visible:outline-*`).
- **No unreplaced outline removal**: Never use `outline: none` or Tailwind `outline-none` without immediately providing a `:focus-visible` replacement.
- **Focus visible over focus**: Use `:focus-visible` instead of `:focus` so click and pointer interactions do not leave persistent focus rings on buttons.
- **Compound controls**: Use `:focus-within` on containers or compound inputs to indicate active state across grouped controls.
- **Unobstructed focus**: Sticky headers, floating toolbars, footers, and modal overlays must never cover or obscure the currently focused element.

---

## 3. Forms & Inputs

- **Autocomplete and naming**: Every form input must declare a meaningful `name` and appropriate standard `autocomplete` attribute (e.g., `email`, `current-password`, `username`, `one-time-code`).
- **Input types and modes**: Use the semantic `type` (`email`, `tel`, `url`, `number`) and appropriate `inputmode` (`numeric`, `decimal`, etc.) to trigger optimal mobile keyboards.
- **Never block paste**: Do not intercept `onPaste` with `preventDefault()` on password, token, or text fields. Users must be able to paste credentials from password managers.
- **Clickable labels**: Ensure labels are clickable and toggle or focus the control, either using `htmlFor="<id>"` or wrapping the input.
- **Spellcheck suppression**: Disable spellcheck on technical, code, email, and username fields (`spellCheck={false}`).
- **Hit targets**: Checkboxes and radio buttons must share a single, unified hit target with their labels (no dead zones between box and text).
- **Submission states**: Keep submit buttons enabled until the request starts; show an inline loading spinner and disable duplicate submissions while in-flight.
- **Inline errors**: Display field validation errors inline next to the affected field. On form submission failure, move focus directly to the first erroneous field.
- **Placeholders**: Placeholders must end with an ellipsis (`…`) and demonstrate example patterns, not act as a replacement for field labels.
- **Non-auth autocomplete**: Set `autocomplete="off"` on non-credential search and filter fields to prevent unwanted password-manager dropdowns.
- **Unsaved changes**: Warn users via `beforeunload` or router guards before navigating away from a dirty form with unsaved changes.

---

## 4. Animation & Motion

- **Honor reduced motion**: Always respect `prefers-reduced-motion` media queries (`@media (prefers-reduced-motion: reduce)` or `motion-reduce:*`). Provide a static alternative or disable transitions.
- **Compositor properties**: Animate only GPU-accelerated compositor properties: `transform` and `opacity`. Avoid animating `width`, `height`, `top`, `left`, `margin`, or `padding`.
- **Never transition all**: Never use `transition: all`. Explicitly enumerate the transitions applied (e.g., `transition-transform`, `transition-opacity`, `transition: opacity 150ms ease`).
- **Transform origin**: Set an explicit and correct `transform-origin` when scaling or rotating elements.
- **SVG transforms**: Apply transforms on `<g>` wrapper elements with `transform-box: fill-box; transform-origin: center;` to avoid cross-browser transform distortion.
- **Interruptible animations**: Transitions and animations must respond immediately to mid-stream user input without hitching or locking.
- **Autoplay control**: Any ambient animation or video playing longer than 5 seconds alongside other content must provide user pause, stop, or hide controls.
- **Muted loops**: Decorative looping animations must stop when `prefers-reduced-motion` is active.

---

## 5. Typography & Text

- **Ellipsis glyph**: Use the single typographic ellipsis character `…` (`&#8230;` / `\u2026`), never three periods `...`.
- **Curly quotes**: Use directional curved quotes `“` `”` and apostrophes `’`, not straight double quotes `"` or straight single quotes `'`.
- **Non-breaking spaces**: Use non-breaking spaces (`&nbsp;` / `\u00A0`) between numbers and units (e.g., `10&nbsp;MB`), keyboard shortcuts (e.g., `⌘&nbsp;K`), and compound brand names.
- **Loading states**: Terminate pending feedback and loading strings with an ellipsis: `"Loading…"`, `"Saving…"`, `"Deploying…"`.
- **Tabular figures**: Use `font-variant-numeric: tabular-nums` (Tailwind `tabular-nums`) for numeric data columns, timers, metrics, and tabular comparisons to prevent jitter.
- **Text wrap**: Apply `text-wrap: balance` on short headings and titles, or `text-wrap: pretty` on paragraphs to prevent typographic widows and awkward breaks.

---

## 6. Content Handling & Resilience

- **Long content handling**: Prevent text overflow in containers using `truncate`, `line-clamp-*`, or `break-words`.
- **Flex child truncation**: Flex children containing truncated text must declare `min-w-0` to allow shrinkage and text truncation.
- **Empty and edge states**: Explicitly handle empty states, null values, and blank arrays—never render broken layouts or raw `undefined`/`NaN`.
- **User-generated content**: Anticipate short, average, and unusually long text strings; test with edge strings and multi-line inputs.

---

## 7. Images & Media

- **Explicit dimensions**: Always declare explicit `width` and `height` (or aspect-ratio styles) on `<img>` elements to eliminate Cumulative Layout Shift (CLS).
- **Lazy loading**: Apply `loading="lazy"` on all images rendered below the fold.
- **Priority images**: Mark above-the-fold hero and critical images with `fetchpriority="high"` (or Next.js `priority`).
- **Video over GIF**: Prefer lightweight `<video autoplay muted loop playsinline>` over animated GIFs. Always supply a still image fallback.
- **Short looping media**: For short non-essential loops, provide an H.264 MP4 source, respect `prefers-reduced-motion`, and supply a poster image.

---

## 8. Performance & DOM

- **List virtualization**: Virtualize long lists exceeding 50 items using virtualization libraries (e.g., TanStack Virtual, `virtua`) or CSS `content-visibility: auto`.
- **No layout thrashing**: Never trigger layout reads (`getBoundingClientRect()`, `offsetHeight`, `offsetWidth`, `scrollTop`) inside active render loops or interleaved with DOM writes.
- **Batch DOM operations**: Group DOM reads before DOM writes.
- **Cheap input handlers**: Keep controlled input handlers minimal per keystroke; debounce heavy searches or filter updates, or prefer uncontrolled inputs with form actions.
- **Asset preconnection**: Add `<link rel="preconnect">` for external CDN, font, and API origins.
- **Font preloading**: Preload critical web fonts with `<link rel="preload" as="font" crossorigin>` and configure `font-display: swap`.

---

## 9. Navigation & Stateful Routing

- **URL as source of truth**: Reflect UI state in URL search parameters (filters, pagination, active tabs, open sheet identifiers) so views are shareable and bookmarkable.
- **Native link behaviors**: Render links using `<a>` or framework `<Link>` components so middle-click, Cmd/Ctrl+click, and context menus function naturally.
- **Deep linking**: Enable deep-linkable URLs for modal drawers, filters, and stateful widgets (e.g., via `nuqs` or URL search params).
- **Destructive confirmations**: All destructive actions (delete, reset, revoke) require an explicit confirmation modal or a generous undo toast window—never trigger irreversible actions immediately.

---

## 10. Touch & Mobile Interaction

- **Touch manipulation**: Set `touch-action: manipulation` on interactive elements to eliminate the 300ms double-tap zoom delay on mobile browsers.
- **Tap highlight**: Intentionally configure `-webkit-tap-highlight-color` to provide clear feedback or remove default gray box overlays.
- **Overscroll containment**: Apply `overscroll-behavior: contain` inside scrollable modals, drawers, slide-overs, and bottom sheets to prevent background scroll chaining.
- **Pointer fine rubber-banding**: If suppressing root rubber-banding, apply `overscroll-behavior: none` on `<html>` only inside `@media (pointer: fine)`. Keep default mobile touch behavior for pull-to-refresh.
- **Drag interactions**: During drag-and-drop operations, disable user text selection (`select-none`) and mark dragging preview elements `inert`.
- **Gesture alternatives**: Any swipe, drag, or pinch gesture must offer a single-tap/click and keyboard-accessible alternative.
- **Judicious autoFocus**: Use `autoFocus` sparingly on desktop only, restricted to the single primary input of a dedicated modal or search page. Avoid on mobile to prevent abrupt keyboard jumps.

---

## 11. Safe Areas & Layout

- **Notch and bar insets**: Full-bleed layouts and fixed headers/bottom bars must accommodate mobile notches using `env(safe-area-inset-top)`, `env(safe-area-inset-bottom)`, etc.
- **Horizontal scroll prevention**: Prevent unintentional page-level horizontal scrollbars with `overflow-x-hidden` on root containers and resolve overflowing inner widths.
- **CSS layout**: Prefer CSS Grid and Flexbox over JavaScript calculations for layout sizing and alignment.

---

## 12. Dark Mode & Theming

- **Dark color scheme**: Set `color-scheme: dark` on `<html>` when dark theme is active so native browser controls, scrollbars, and inputs render with matching system dark styling.
- **Theme color meta**: Keep `<meta name="theme-color">` synchronized with page background color across themes.
- **Native controls in dark mode**: Explicitly set `background-color` and `color` on native `<select>` and input options for consistent rendering on Windows high-contrast and dark modes.

---

## 13. Locale, Numbers & Dates

- **Intl date/time**: Format dates and timestamps with `Intl.DateTimeFormat` or localized date libraries, never hardcoded date strings.
- **Intl numbers/currency**: Format currencies and numbers with `Intl.NumberFormat`, never manual string concatenations.
- **Language detection**: Determine user language preferences from `Accept-Language` headers or `navigator.languages`, not coarse IP geolocation.
- **Translation suppression**: Wrap brand names, code snippets, tokens, and system identifiers in `translate="no"` (or `class="notranslate"`) to prevent browser auto-translation corruption.

---

## 14. Hydration Safety

- **Controlled inputs**: Inputs specifying a `value` prop must provide a companion `onChange` handler, or use `defaultValue` for uncontrolled state.
- **SSR time guards**: Avoid rendering dynamic timestamps directly in SSR markup that cause client hydration mismatches; format them client-side or use stable ISO markers.
- **Minimal hydration suppression**: Limit `suppressHydrationWarning` strictly to the individual element containing unavoidable client/server variations (e.g., dynamic timestamps).

---

## 15. Hover & Interactive States

- **Visual hover states**: Buttons, links, and cards must declare clear `:hover` styles to confirm interactivity.
- **Contrast progression**: Interactive states must show progressive visual hierarchy: `:hover`, `:active`, and `:focus-visible` should increase contrast and clarity relative to the resting state.

---

## 16. Content & Copy

- **Active voice**: Use direct, active imperatives: *"Install the CLI"* instead of *"The CLI will be installed"*.
- **Title case**: Apply Title Case for headings, page titles, and primary action buttons (Chicago style).
- **Numerals**: Use Arabic numerals for counts and statistics: *"8 deployments"* instead of *"eight deployments"*.
- **Descriptive button labels**: Use specific action verbs: *"Save API Key"*, *"Create Project"*, *"Delete Workspace"* instead of generic *"Submit"*, *"Continue"*, or *"OK"*.
- **Actionable errors**: Error messages must state what went wrong and provide the immediate next step or fix.
- **Second person**: Address the user directly using second person (*"your project"*), avoiding first-person declarations (*"we"* / *"I"*).
- **Ampersands**: Use `&` instead of *"and"* only when space is constrained (e.g., badges, breadcrumbs, tight tables).

---

## 17. Anti-Patterns Checklist

Flag and fix the following anti-patterns during review:

- [ ] `user-scalable=no` or `maximum-scale=1` in viewport meta (blocks user zoom accessibility).
- [ ] `onPaste` handler with `preventDefault()` (blocks password managers and legitimate pasting).
- [ ] `transition: all` or unlisted CSS transition properties.
- [ ] `outline-none` / `outline: none` without a matching `:focus-visible` replacement.
- [ ] `<div>` or `<span>` with click handlers instead of semantic `<button>` or `<a>`.
- [ ] Inline click handlers performing navigation instead of `<a>` or `<Link>`.
- [ ] `<img>` tags missing explicit `width`, `height`, or `alt` attributes.
- [ ] Large list rendering (`.map()`) with >50 items without virtualization or `content-visibility: auto`.
- [ ] Inputs without accessible labels or missing `autocomplete` attributes.
- [ ] Icon-only buttons lacking `aria-label`.
- [ ] Hardcoded date, time, or currency strings instead of `Intl.*`.
- [ ] Unjustified or mobile `autoFocus` triggering viewport jumps.
- [ ] Animated GIF used where compressed MP4 video is appropriate.
- [ ] Gesture-only actions without single-click/tap and keyboard alternatives.

---

## 18. Audit & Review Output Format

When auditing files for Web Interface Guidelines compliance, produce concise, high signal-to-noise reports grouped by file using the clickable `file:line` format:

```text
## src/components/Button.tsx

src/components/Button.tsx:42 - icon button missing aria-label
src/components/Button.tsx:18 - input lacks accessible label
src/components/Button.tsx:55 - animation missing prefers-reduced-motion guard
src/components/Button.tsx:67 - transition: all → list explicit properties

## src/components/Modal.tsx

src/components/Modal.tsx:12 - missing overscroll-behavior: contain on dialog body
src/components/Modal.tsx:34 - straight ellipsis "..." → typographic glyph "…"

## src/components/Card.tsx

✓ pass
```
