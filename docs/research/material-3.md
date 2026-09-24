# Material 3 and Material 3 Expressive

Research for a Material 3 Expressive design skill for Jetpack Compose. The goal
is the equivalent of `apple-design` for Android: design judgment distilled from
Google's own sources, with concrete values and Compose APIs, so generated
screens embrace Expressive instead of looking like the stock M3 catalog.

Expressive is not a separate design language. Google calls it an evolution of
M3 ("not M4") and an "expansion pack" of opt-in APIs, so this document covers
the M3 foundation first ([Part 1](#part-1-material-3-foundation)) and the
Expressive layer on top of it ([Part 2](#part-2-the-expressive-layer)). Wear OS
lives in [material-3-wear-os.md](material-3-wear-os.md).

## Sources and status

- **m3.material.io**, full text of content version `2026-09-16_06-10-03`: every
  page under Get started, Foundations, Styles, Components, and Develop, plus the
  four Expressive articles. Pages come from the site's content API at
  `https://m3.material.io/_dsm/content/m3/<version>/<page-id>.json`; the page
  manifest is embedded in the site's Angular bundle. The 110 pre-Expressive
  blog posts (2020 to 2024) were skipped.
- **developer.android.com Wear OS design guide**, "Benefits of expressive
  design": reproduces Google's research write-up on Expressive and is used here
  for the research findings.
- **Six official Android Developers videos** (manual transcripts):
  "Build next-level UX with Material 3 Expressive" (I/O 2025, `6IsFP3gD28E`),
  "More customization in Material 3: the path to expressive apps" (Nov 2025,
  `t9rrsqfB2tM`), "Build beautiful, premium, adaptive apps with Material"
  (I/O 2026, `zRBi6oBtpoo`), "Make Material your own" (I/O 2026,
  `HbAFGivZ158`), "The future unfolds! How to optimize Android apps for
  adaptive layouts" (Jul 2026, `pLNJ-fNYTKU`), and "WearOS Material 3 shape
  morphing" (Jul 2025, `qEEo6AwgBjU`).
- **androidx sources** at commit `bf95ca5` (Sep 22, 2026), the released
  `material3-android-1.5.0-alpha28` sources jar, and Maven metadata. See
  [Compose API reference](#compose-api-reference-verified-september-2026).

The most valuable pages for the skill:

| Page | Why it matters |
| --- | --- |
| [Start building with M3 Expressive](https://m3.material.io/building-with-m3-expressive) | The seven expressive tactics and hero moments |
| [Usability, Applying M3 Expressive](https://m3.material.io/foundations/usability) | Worked example (Aura app) of emphasis decisions, with before/after |
| [Shape](https://m3.material.io/styles/shape) | Shape principles, tension, morph, optical roundness |
| [Typography](https://m3.material.io/styles/typography) | Emphasized styles, brand/plain typefaces, editorial treatments |
| [Motion physics](https://m3.material.io/styles/motion/overview) and [with Compose](https://m3.material.io/m3-expressive-motion-theming) | Spring schemes, spatial vs effects, `MotionScheme` |
| [Color roles](https://m3.material.io/styles/color/roles) and [advanced](https://m3.material.io/styles/color/advanced) | Role semantics, combining schemes, fidelity |
| [Breakpoints](https://m3.material.io/foundations/layout/breakpoints) and [scaffold](https://m3.material.io/foundations/layout/scaffold) | Adaptive layout rules |
| Component pages | Usage rules, and what Expressive replaced |

## Why the stock output looks generic

Google's own research team started from the same complaint. In 2022 a research
intern studying sentiment toward Material in Google apps triggered a team-wide
debate: "Why did all these apps look so similar? So boring? Wasn't there room
to dial up the feeling?" Expressive is their answer. Three causes show up when
comparing the guidelines with typical generated code:

1. **Stock M3 is the baseline, not the target.** Expressive is opt-in:
   `MaterialExpressiveTheme`, emphasized type styles, `MotionScheme.expressive()`,
   the shape library. Code using `MaterialTheme`, default type and shapes, and
   baseline components produces pre-2025 Material.
2. **Many default components are no longer recommended.** Medium and large top
   app bars, the bottom app bar, segmented buttons, the navigation drawer,
   baseline lists and menus, the small FAB, and the circular spinner for short
   waits were all replaced (see [the table](#components-what-expressive-replaced)).
3. **Expressive is about emphasis decisions, which tokens cannot encode.** The
   guidance is about choosing one primary goal per screen and spending emphasis
   (size, color contrast, shape, type, containment, motion) on it. Without that
   decision, every element gets equal weight.

---

# Part 1: Material 3 foundation

## Tokens and theming model

- Tokens replace hardcoded values. Three classes: **reference** (`md.ref.*`,
  every available value, like `palette.secondary90`), **system** (`md.sys.*`,
  the decisions that make a theme, such as `color.secondary-container`; this
  is where theming happens), and **component** (`md.comp.*`, like
  `fab.primary.container.color`).
- Tokens resolve per **context**: light or dark theme, contrast level, density,
  form factor, RTL. Hardcoded values break every one of these.
- In Compose, system tokens surface as `MaterialTheme.colorScheme`,
  `.typography`, `.shapes`, and `.motionScheme`.

## Color

### How the system works

- "Paint by number": UI elements are assigned **roles**; roles get colors.
- A source color (wallpaper, in-app content, or hand-picked) runs through
  Material Color Utilities, which derives five key colors (primary, secondary,
  tertiary, neutral, neutral variant), builds a tonal palette for each (tones
  0 to 100), and assigns tones to 26+ roles for light and dark themes.
- Color math uses **HCT** (hue, chroma, tone). Tone determines contrast; hue and
  chroma can change without affecting it. Don't reason in HSL.
- **Three contrast levels** (May 2025): standard, medium (at least 3:1), high
  (7:1). Using roles correctly gives these for free, including in custom
  components.
- Aug 2024: `on*Container` roles became more colorful in light theme.

### Roles

| Group | Roles | Use |
| --- | --- | --- |
| Primary | primary, onPrimary, primaryContainer, onPrimaryContainer | Most important actions and elements: FAB, high-emphasis buttons, active states |
| Secondary | secondary, onSecondary, secondaryContainer, onSecondaryContainer | Less prominent: filter chips, selected nav icon, tonal buttons, dismissive actions |
| Tertiary | tertiary, onTertiary, tertiaryContainer, onTertiaryContainer | Contrasting accents that balance primary and secondary, or heightened attention on smaller elements (badges, input fields); "at the designer's discretion... to support broader color expression" |
| Error | error, onError, errorContainer, onErrorContainer | Static in every scheme (still adapts to light and dark) |
| Surface | surface, onSurface, onSurfaceVariant, surfaceContainerLowest to surfaceContainerHighest, surfaceDim, surfaceBright | Backgrounds and large low-emphasis areas; containers for cards, sheets, dialogs |
| Outline | outline, outlineVariant | outline for important boundaries (text fields); outlineVariant for dividers and decorative lines |
| Inverse | inverseSurface, inverseOnSurface, inversePrimary | Snackbars and other reversed elements |
| Fixed (add-on) | primaryFixed, primaryFixedDim, onPrimaryFixed, onPrimaryFixedVariant (and secondary, tertiary) | Same tone in light and dark; avoid where contrast against surface is needed |

Rules:

- **Containers are fills**, never text or icon colors. **Only pair `X` with
  `onX`** (or `onSurface` / `onSurfaceVariant` on any surface). Other pairings
  break under dynamic color and contrast levels.
- Default text is `onSurface`; `onSurfaceVariant` for lower emphasis. Links use
  `primary` (or `tertiary` for less prominent), always underlined.
- `surface` for the body and `surfaceContainer` for navigation regions, the
  same mapping at every breakpoint. The five container levels create nesting
  and hierarchy on larger screens. Default mappings: low (elevated button and
  card), default (top and bottom bars), high (FAB, dialog), highest (text field
  fill, switch track).
- Don't use `outline` for dividers or multi-element containers like cards; don't
  use `outlineVariant` alone to define a tap target's boundary.
- `surfaceBright` / `surfaceDim` keep relative brightness in both themes (for
  example a dim rail beside a bright chat pane).

### Choosing a scheme

| Scheme | Choose when | Result |
| --- | --- | --- |
| Static baseline | Not ready for dynamic, enterprise, iOS | The stock purple Material look |
| Static custom brand | Product should "look like its brand" | Hand-picked sources for primary, secondary, tertiary, neutral; consistent everywhere |
| Dynamic, user-generated | Showcase personalization | Colors from the wallpaper; the app looks like every other app on that phone |
| Dynamic, content-based | Content is front and center (media, photos) | Colors from album art or images, applied near the source |

- Dynamic color can be applied selectively, for example only on a profile
  screen.
- **Combine schemes:** a base scheme (baseline, brand, or wallpaper) plus one
  content-based scheme mapped to contained areas (a media player colored by its
  album art; each activity card colored by its image). Limit a screen to **two
  scheme source types**. Keep the source image visible where its color is used.
  Don't replace semantic colors (a red error, a green success) with content
  color.
- **Static colors** (formerly custom colors): add semantic colors like Success;
  Material returns four roles (color, on, container, onContainer). Optionally
  **harmonize** them toward primary's hue; skip harmonizing for literal brand
  colors.
- **Custom baseline:** by convention, primary and tertiary are the most
  prominent, tertiary shifting hue from primary; secondary, neutral variant,
  and neutral share primary's hue with progressively less chroma.
- **Color fidelity** keeps generated tones close to the input color (otherwise a
  dark purple can come back pale). It's on by default in Theme Builder for
  custom and static colors.
- **Custom dynamic scheme:** to match the wallpaper but look more vibrant,
  define your own hue and chroma rules and generate through Material Color
  Utilities.

## Typography

- **Roles:** Display, Headline, Title, Body, Label, each Large / Medium /
  Small. Display: short, important text or numerals, best on large screens.
  Headline: short high-emphasis text on small screens. Title: medium emphasis,
  secondary regions. Body: long passages. Label: text inside components (buttons
  use Label Large), captions.
- Default scale: Major Second (1.125) anchored at 14. Aim for "impactful
  contrast between sizes by avoiding small differences." Products rarely need all
  15 styles; a reduced set of about 5 is normal.
- **Brand and plain typefaces:** brand for large styles (Display, Headline),
  plain for small ones (Body, Label). Roboto is the default for both. "Consider
  replacing Roboto with different typefaces to boost brand expression." Display
  can take an expressive face (Google's examples: Bagel Fat One, Anton);
  Headline can with adjusted line height and tracking; be cautious at Title;
  never on Body or Label.
- Line height about **1.2x** for Title, Headline, Display and about **1.5x** for
  Body and Label. Heavier fonts may need wider tracking.
- **Tabular numbers** wherever values change (clocks, timers, tables).
- Keep text at **40 to 60 characters per line** across breakpoints.
- Variable fonts: Roboto Flex (width, weight, grade, slant, optical size, plus
  parametric axes), Roboto Serif, Roboto Mono; Google Sans Flex has six axes.
  Fallback order: Roboto Flex, Roboto, Noto Sans.
- Language height (Aug 2026): line heights adapt to script height (small,
  medium about 7% taller, large about 30%, extra large about 100%). Default to
  medium.
- Fonts are `sp` on Android. Support **200% text scaling**: text and line
  height scale, padding and spacing stay fixed, layouts reflow (stack
  side-by-side buttons, allow vertical scrolling).

## Shape

Corner radius scale (10 steps; the three marked Expressive were added May 2025):

| Token | Value | Typical use |
| --- | --- | --- |
| None | 0dp | |
| Extra small | 4dp | Chips, snackbars |
| Small | 8dp | Text fields, menus |
| Medium | 12dp | Cards |
| Large | 16dp | FABs, drawers |
| Large increased | 20dp (Expressive) | |
| Extra large | 28dp | Dialogs, bottom sheets |
| Extra large increased | 32dp (Expressive) | |
| Extra extra large | 48dp (Expressive) | |
| Full | fully rounded | Buttons, badges |

- Customize at the **style** level (change what `medium` means everywhere) or the
  **component** level (remap buttons from `full` to `medium`). Families can be
  rounded or cut.
- **Optical roundness:** nested radius = outer radius minus padding (48dp minus
  14dp = 34dp). Never reuse the outer radius inside.
- **Inner corners:** closely grouped items (menus, split buttons, connected
  button groups, segmented lists) use small inner and large outer corners.
- Don't put large or full corners on information-dense components like cards.
- Expressive shape guidance is in [Part 2](#shape-expressive).

## Elevation

- Six levels: 0 (0dp), 1 (1dp), 2 (3dp), 3 (6dp), 4 (8dp), 5 (12dp). Resting
  states use 0 to 3; 4 and 5 are for hover and drag. Hover raises by one level.
- M3 shows elevation with **tonal surface differences** by default. Use visible
  **shadows only** to protect elements over busy backgrounds or to invite
  interaction (lift on drag). "The fewer levels in your UI, the more power they
  have." Don't change components' default elevation.
- Overlapping containers must use different surface roles.
- **Scrims** use the `scrim` role at 32% opacity behind modals and expanded
  navigation.
- Resting levels: 3 for FAB, extended FAB, dialogs, pickers, search; 2 for
  scrolled app bar, menus, nav bar, toolbars, rich tooltips; 1 for modal sheets,
  elevated buttons and cards, banners; 0 for most everything else (filled,
  tonal, outlined buttons; filled and outlined cards; lists; rails; tabs).
- Surface tint is deprecated.

## Icons

- **Material Symbols** (variable font) in outlined, rounded, or sharp. Pick the
  style that echoes the brand: rounded with heavy type and curved shapes, sharp
  with rectangular details, outlined for dense UI.
- Axes: **weight** (minimum 200 at 24dp; apply consistently, never mix weights
  in one set), **fill** (0 to 1; use filled for selected states), **grade**
  (-25 for light icons on dark backgrounds; positive grade for active states),
  **optical size** (20 to 48dp; 40 to 48dp to highlight primary actions).
- Match icon size and optical weight to adjacent text; shift the icon baseline
  down about 11.5% of the text size.
- 24dp icons get 48dp touch targets. Icons below 20dp that are complex or key
  actions need a text label.
- Toggle icon buttons: outlined when unselected, filled when selected (semibold
  if no filled version exists).

## Layout

### Breakpoints

| Breakpoint | Width | Panes | Navigation | Margins |
| --- | --- | --- | --- | --- |
| Compact | under 600dp | 1 | Navigation bar or modal expanded rail | 16dp |
| Medium | 600 to 839dp | 1 (recommended) or 2 | Navigation bar (2 panes) or collapsed rail (1 pane) | 24dp, 24dp spacer |
| Expanded | 840 to 1199dp | 2 (recommended) | Collapsed or expanded rail | 24dp, 24dp spacer |
| Large | 1200 to 1599dp | 2 (recommended) | Rail, collapsed or expanded | 24dp, 24dp spacer |
| Extra large | 1600dp+ | 1 to 3 | Expanded rail | 24dp, 24dp spacer |

- Design for breakpoints, not devices. Height breakpoints exist but are rarely
  needed.
- Moving up a breakpoint, ask: **reveal** (show the expanded rail, a second
  pane), **divide** (panes), **resize** (cards, feeds, lists, keep 40 to 60
  characters per line), **reposition** (move actions from bottom to leading
  edge, add negative space), **swap** (nav bar to rail, bottom sheet to menu,
  full-screen dialog to basic dialog). Only swap functionally equivalent
  components; "Additional space doesn't just mean making the same thing
  bigger."
- Medium two-pane layouts: only for low-density content, 50/50 widths.
- Fixed-and-flexible layouts: fixed pane 360dp (expanded) or 412dp (large and
  up). Split-pane keeps the spacer visually centered even with a rail.
- Reachability on tablets in landscape: avoid interactions in the top 25%.

### Scaffold

- **Bars** (app bar at top, navigation bar at bottom) frame the page beside the
  safety regions (system UI). **Rails** surround panes: on compact, the rail
  region above the nav bar holds toolbars, chat inputs, FABs; on large screens
  the leading rail holds the navigation rail and the trailing rail holds
  supporting controls. **Panes** hold all content, 1 to 3 of them, at least one
  flexible.
- Panes display as **co-planar** (side by side), **floating** (dialog-like), or
  **docked** (bottom sheet). They adapt by **show and hide**, **levitate**
  (become floating or docked), or **reflow** (stack vertically).
- **Canonical layouts:** **feed** (card grid, 1 column compact, more columns up),
  **list-detail** (1 pane compact, 2 panes expanded; selection state only in
  two-pane, back button only in single-pane; keep scroll position), and
  **supporting pane** (primary about two-thirds; below the focus pane on compact
  and medium, beside it at 360dp on expanded).
- Containment: panes can blend with the background (implicit grouping) or use
  color and outlines (explicit). In multi-pane layouts, use color to show
  emphasis.

### Spacing

- 8dp system (`space100` = 8dp; Compose only); nested units like 2, 4, 6, 10dp
  exist. Prefer **padding and gaps** on the parent over margins on children.
- Spacing groups (explicit via containers and outlines, implicit via
  proximity), directs attention (rhythm, similarity, proximity, continuity),
  and sets personality: "A denser layout can feel more serious and focused,
  while a more spacious layout can feel calm and open."
- Density is a user choice, not an automatic breakpoint change. Never raise
  density on focused tasks (menus) or alerts (snackbars, dialogs). Component
  density drops 4dp per step.

## Interaction

- **States:** enabled, disabled, hover, focused, pressed, dragged. State layers
  use the content (`on*`) color at hover 8%, focus 10%, press 10%, drag 16%.
  State layer 40dp inside a 48dp target. Only one hover, focus, press, or drag
  at a time. Disabled needs no contrast; a FAB whose action is unavailable
  should disappear rather than disable.
- **Selection:** check marks, checkboxes, or a surface color change; nav bar,
  rail, and tabs use an active indicator. Enter selection mode with long press
  (or tapping an avatar); long press plus drag for batch selection.
- **Gestures:** tap, double tap, long press, scroll, swipe (peer views or
  actions), drag, pick up and move, pinch. **Predictive back** is supported on
  bottom sheets, nav bar, nav rail, search, and side sheets.
- **Inputs:** support mouse, trackpad, and keyboard on every form factor: hover
  states, context menus on secondary click, Enter to send, Space to play,
  Escape to dismiss, logical Tab order.

## Motion foundation: transitions

Transitions still use the legacy easing and duration system (the springs in
[Part 2](#motion) drive components).

- **Six patterns:** container transform (strongest relationship, "perceived to
  be the most expressive"; cards, list items, FABs, search), forward and
  backward (Android: fade while sliding), lateral (peers slide in unison, no
  fade), top level (fade out then in, no shared elements), enter and exit
  (Android components expand along x or y, away from the nearest edge, not
  scale), skeleton loaders.
- Good transitions: respect reduced motion (fades, no parallax or morphing),
  stay consistent, keep layouts stable, avoid jump cuts (unless efficiency
  matters most), build a coherent spatial model, move in a **unified direction**
  (only hero elements persist), use **clean fades** (fade out fully before
  fading in; never fade a bottom sheet), and keep a **simple style** ("not
  receptive to highly stylized motion").
- Easing: Emphasized for most transitions (Standard for small utility ones);
  Emphasized decelerate to enter, Emphasized accelerate to exit permanently.
  Duration scales with the area covered; exits are shorter than enters.

| Easing | Cubic bezier |
| --- | --- |
| Emphasized | 0.2, 0, 0, 1 |
| Emphasized decelerate | 0.05, 0.7, 0.1, 1 |
| Emphasized accelerate | 0.3, 0, 0.8, 0.15 |
| Standard | 0.2, 0, 0, 1 |
| Standard decelerate | 0, 0, 0, 1 |
| Standard accelerate | 0.3, 0, 1, 1 |

## Accessibility

- Contrast: 4.5:1 small text, 3:1 large text and graphics. Clustered elements
  (a row of buttons) need 3:1 against the background; standalone prominent ones
  (a FAB) don't.
- Targets 48x48dp (pointer 44dp), 8dp between targets.
- Labels describe purpose, not appearance ("Voice search," not "Microphone"),
  and never include the role ("button"). Mark decorative images as hidden.
- Define initial focus and focus return (dialogs return focus to their
  trigger). Keyboard shortcuts use two or more keys.
- Use native components before custom ones; custom dialogs need extra testing.

## Content design

- Sentence case everywhere, including buttons and app bars. No all caps.
- Second person ("you"); don't mix with "my"; avoid "we."
- No periods on single sentences in labels, tooltips, lists, dialog body text.
  Contractions yes. Serial comma. Exclamation points only for real
  celebrations. No ellipses on buttons or menu items. Avoid em dashes; use an
  en dash without spaces for ranges.
- Explain consequences neutrally and how to undo.
- Global writing: short sentences, no idioms, no abbreviations, avoid
  "please," "sorry," "thank you" in errors.
- Notifications: lead with what matters to the user, title under 29
  characters, collapsed body under 40, 1 or 2 buttons, days of the week instead
  of "today," don't repeat the app name, "don't overdo delight."

## Component selection guide

Condensed "which component and when" rules from the component usage sections.

| Need | Use | Key rules |
| --- | --- | --- |
| Page title and 1 or 2 actions | App bar (search, small, medium flexible, large flexible) | 1 action, 2 max; boost the key action with a filled or tonal (optionally wide) icon button, never two filled; container fills on scroll, or stays transparent with filled icon buttons; flexible bars compress to small on scroll |
| Many page actions | Toolbar (docked for global, floating for contextual); "Where app bar supports navigation, toolbar provides critical actions for the current page" | Standard or vibrant color; never with a navigation bar on screen; floating can collapse to a FAB on scroll |
| The single most important action | FAB (the site calls medium "most recommended"; the talks position it for compact and medium windows and foldables) | One per screen; not every screen needs one; stays put on scroll; disappears and reappears when switching tabs |
| Labeled primary action on long scroll | Extended FAB (small, medium, large) | One per screen; bottom half only; not in a set of actions; not with a floating toolbar |
| 2 to 6 related actions from a FAB | FAB menu | Color set matches the FAB; not from an extended FAB; not with a toolbar or rail |
| Discrete actions | Buttons (elevated, filled, tonal, outlined, text; XS to XL; round or square) | Don't overuse; no more than 3 in an arrangement; the primary action gets more size, color, or shape |
| Related buttons that react together | Standard button group | Same size and shape by default; mixed sizes only for hero moments; shape differences only for selection or meaning |
| Pick options or switch views | Connected button group | Must be toggleable; don't mix color styles |
| Main action plus hidden alternatives | Split button | Menu aligned to the trailing half, 4dp gap; menu button rotates with the standard scheme |
| Common icon actions | Icon button (filled, tonal, outlined, standard; XS 32 to XL 136dp; narrow, default, wide) | Filled sparingly; same size when equally important; tooltip on hover |
| Contextual choices, filters, tags | Chips (assist, filter, input, suggestion) | Never for Save or Cancel; always in a set; scroll horizontally |
| Single-topic content | Cards (elevated, filled, outlined; same function) | Don't force content into cards when spacing or headings would do; expand with container transform only for hero moments; no internal scrolling on mobile |
| Visual collection | Carousel (multi-browse, uncontained, hero, center hero, full-screen) | Snap-scroll except uncontained; max 3 items with text on compact; provide Show all |
| Scannable items | List (expressive) | Align leading visuals and text; gaps for contained lists, dividers only for uncontained; one selection mode at a time |
| Temporary actions | Menu (vertical) | Vibrant sparingly; group with gaps or dividers |
| 3 to 5 top destinations | Navigation bar (flexible) | Labels always; not with fewer than 3 (use tabs); no swiping between destinations; reselect scrolls to top |
| Destinations on larger screens | Navigation rail (collapsed 3 to 7 items; expanded replaces the drawer) | Leading edge, outside panes; one navigation component per screen |
| Related content at one level | Tabs (primary, secondary) | Not for sequential content; avoid swipeable content inside |
| Supplementary content | Bottom sheet (mobile), side sheet (medium+) | Drag handle cycles heights; scrim tap closes |
| Blocking decision | Dialog | Sparingly; low priority goes to a snackbar |
| Brief feedback | Snackbar | One at a time; one action; above the FAB and navigation |
| Wait 200ms to 5s | Loading indicator | Under 200ms nothing; over 5s a progress indicator; one indicator per group |
| Settings on or off | Switch | Immediate effect; not for opposing options (use a connected button group) |
| One or many from a list | Radio buttons (5 or fewer options) or checkboxes | Vertical; one radio always selected |
| Value from a range | Slider (standard, centered, range; XS to XL) | Immediate effect; no vertical range sliders |
| Text entry | Text field (filled or outlined) | Don't mix variants in one form |
| Search | Search bar, search app bar (global), or search icon button (secondary) | Gaps group results; docked on tablets, full-screen on phones |
| Icon-only label | Plain tooltip | One at a time; never hide critical info in a tooltip |

---

# Part 2: The Expressive layer

## What it is and why

> "Expressive interfaces have an emotional impact, fostering connection by
> evoking a feeling or mood through visual design and interaction."

- Launched May 2025 as "a set of new features, updated components, and design
  tactics for creating emotionally impactful UX." Not M4, not a deprecation.
- "Most researched update since 2014": 46 studies, 18,000+ participants, using
  eye tracking, surveys and focus groups, experiments, and usability tests.
  Research started at the component level (which progress indicators make
  waits feel faster, how big a button can be before it overwhelms, how to make
  the floating toolbar noticeable).
- Findings:
  - Preferred by all ages, **especially 18 to 24**.
  - Rated higher on "energetic," "emotive," "positive vibe," "creative,"
    "playful," "friendly," and seen as more modern.
  - About **100% increase in preference** and **up to 170% in aesthetics** over
    baseline variants.
  - Participants spotted key UI elements **up to 4x faster**.
  - Users are more likely to switch to products using Expressive.
  - Up to **87% preference** among 18 to 24 year olds.
  - Accessibility: older adults usually take longer to find key elements, but
    "with M3 Expressive, we saw a dramatic erasure of the age gap." Larger
    buttons and clearer hierarchy help across movement and visual abilities.
- Expressive sharpens screens that already work: Google's Fitbit before and
  after started from a screen that was "very functional and readable" and used
  the shape library and a toolbar "to strengthen the hierarchy and guide your
  attention." Google ships these in Meet (button groups), the Pixel media
  player (wavy progress), Pixel volume (sliders), Workspace (split button),
  Gmail and Fitbit (shapes), and the Phone dialer (a hero moment).
- The research framing matters for the skill: expressive is justified by
  **usability**, not decoration. Emphasis makes the primary action findable.
- Google's own caveats: "a strong minority of users preferred calmer, less
  intense versions"; start from user needs; "don't compromise your product's
  core functionality for visual flourishes. No amount of emotion can compensate
  for a lack of clarity."
- From the original design.google article ("Expressive Design: Google's UX
  Research"):
  - Eye tracking across 10 apps in Expressive and current M3 versions. In an
    email app, a larger Send button placed just above the keyboard in the
    secondary color was spotted 4x faster than a small Send in the top app bar.
  - Desirability: +32% "subculture" (feels in-the-know), +34% modernity, +30%
    "rebelliousness" (bold, breaks convention).
  - "Context still matters": what works in a media player may not suit
    banking. Failures: a playlist rebuilt as helter-skelter album art looked
    exciting but wasn't recognized as a playlist; removing text labels from
    email actions reduced usability. "When basic interaction paradigms are
    broken, expressive design can lead to poor usability."
  - Unfamiliarity lowered some scores, expected to fade as adoption grows.

## The seven expressive tactics

Each tactic is one axis along which a screen can become more expressive.

1. **Use a variety of shapes.** Mix classic and abstract shapes, round and
   square corners, for tension and contrast. Break from the surrounding shape
   style to draw attention to one element. Caution: smaller shapes can make
   essential actions look less important.
2. **Apply rich and nuanced colors.** Use contrast between primary, secondary,
   and tertiary roles and surface tones to prioritize. Caution: without
   contrast, elements blend together.
3. **Guide attention with typography.** Emphasized styles for headlines and
   actions; heavier weight, larger size, color, and spacing create
   "editorial-like moments."
4. **Contain content for emphasis.** Group content into containers. Give the
   most important content "ample space and the brightest surface mapping."
   Caution: ungrouped information blends together.
5. **Add fluid and natural motion.** Shape morph, surface effects, expressive
   springs, custom micro-animations.
6. **Leverage component flexibility.** Shift controls to the context, adapt to
   foldables and large screens.
7. **Combine tactics to create hero moments.** See below.

### Hero moments

> "Hero moments use multiple expressive tactics to break from predictable or
> uniformly applied design ideas."

- They make a stand-alone statement or frame essential information "in a
  fresh, editorial way," and act as a focusing mechanism: "Invest your time in
  making the most critical interactions sing; these moments are the heart of
  your product."
- They are "brief, delightful, surprising, and unexpected."
- **Stick to one or two per product.** Too many overwhelm or distract.
- To find the hero moment, ask: *Is this interaction emotionally impactful?*
  and *Is this a key interaction in the product?*

## Emphasis and hierarchy (usability)

The usability page is the most actionable source:

- Emphasize key actions to create visual hierarchy; don't overwhelm with too
  much visual information; test and iterate.
- Order of work: first build a strong hierarchy (color, size, spacing,
  placement, containment), **then** add unique emphasis to celebrate success or
  progress (illustration, scale, shapes, shape morph).
- **Size:** the most important action should be the largest element. Larger
  key actions measurably improve efficiency, errors, learnability, and
  satisfaction.
- **Color:** use contrasting hues (their example: purple and green) rather than
  similar ones.
- **Primary goals get the strongest emphasis.** Rank primary, secondary, and
  tertiary goals per screen. One primary task per page, empty space to focus
  attention, core actions large and reachable.
- **Don't use too many expressive tactics at the same time.**

### Worked example: Aura breathing app

Google's conceptual app, built from the eye tracking research. It is the best
template for how the skill should reason: each screen spends emphasis according
to its goal rank.

| Screen | Goal rank | How emphasis is spent |
| --- | --- | --- |
| Home | Primary: start a session | Extra large **Start breathing** button, dark `primary` on soft light purple, placed low for reach, last in the vertical flow. Settings in `secondary`, less emphasized. Daily message least emphasized, but in a soft blue container with medium text. Components: XL button, button groups, switch, navigation bar. |
| Session | Hero moment | A huge flower shape (Material shapes "flower" and "sunny") expands and contracts with the breath, driven by spring tokens. Vibrant yellow on inhale against the purple background. Countdown numbers very large versus small inhale/hold/exhale labels. Pause and stop small, at the bottom. Navigation bar hides. |
| Report | Secondary | Fewer tactics "to reduce cognitive load." Metrics inside uniform flower shapes, emphasized type for key numbers (18, 3min), medium (not XL) **Finish** button. |
| Progress | Tertiary | Subtle tactics. Key data in `primary`, completed days as `secondary` yellow flower shapes in a calendar. Custom-scaled numbers "large enough to scan, but they don't dominate." |

Lessons they call out explicitly:

- **Version 1 vs 2:** v1 had no containment, similar sizes, inconsistent colors,
  every setting competing. v2 grouped settings as list items above one extra
  large button with consistent secondary color. "From cluttered to calm."
- **Clear scale and placement.** Caution: all elements large and competing. Do:
  one strong focal point, supporting controls clear but smaller.
- **Consistent color roles.** Caution: `primary` and `primaryContainer` for
  everything. Do: `primary` for the main action, `secondary` for selected
  settings, `secondaryContainer` for dates.
- **Calm, balanced layouts.** Caution: shapes of different forms and sizes,
  overlapping. Do: uniform shapes, even spacing.
- **One moving thing.** Caution: text width changing while arrows and the
  flower animate at once. Do: keep text stable so attention stays on the one
  moving shape.

## Shape (Expressive)

- 35 shapes in the Material shape library, with built-in morphing between any
  two. Compose: shapes API (`MaterialShapes`); not available on web.
- Shapes echo Google Sans Flex's roundness: "Use shape and type together."

Principles:

- **Morph to connect function and feeling.** Morph for interaction states
  (selected, pressed), actions in progress (typing, loading), and environmental
  change (sound, temperature, time). Consider tap, swipe, scroll, release, long
  press.
- **Be bold and embrace tension.** "Material historically focused on rounded
  shapes. However, using sharp shapes, thereby adding tension, creates more
  dynamic design, one that's more memorable and expressive." Mix square and
  round.
- **Shape is versatile, not semantic.** Don't assign a fixed meaning to one
  shape (a wave is not "the progress shape").
- **Use abstract shapes sparingly.** "Shapes without clear meaning behind why
  they're different can add more visual clutter than delight." Not on
  text-heavy containers.
- **Aesthetic moments are the most flexible use:** image crops, avatar masks,
  decorative graphics.
- **Shape can be 2.5D:** layered shapes with different motion suggest depth.
- Customizing shapes "is sometimes necessary, and even encouraged, for hero
  moments or custom components." Carousels can switch between square and fully
  rounded items for "unexpected moments."

## Color (Expressive)

- "Vibrant color schemes": an expanded range of colors "to sharpen hierarchy
  and clarify key actions."
- Hierarchy comes from **contrast between roles**, not `primary` everywhere.
  Use contrasting hues for different kinds of things (action vs status vs
  data).
- The most important content gets the brightest surface.
- **Vibrant** component styles (menus, toolbars) map to tertiary; "should be
  used sparingly."

## Typography (Expressive)

- 30 styles: 15 **baseline** and 15 **emphasized** (higher weight, minor
  adjustments), same scale. **Components don't use emphasized styles by
  default;** opt in per usage.
- Use emphasized styles on selection, actions, headlines, editorial moments,
  badges, primary buttons, extended FABs, selected list and menu items, unread
  messages.
- Two ways to apply: **weight** (emphasize text that is already bold) and
  **context** (emphasize selectively to show hierarchy or state).

### Google Sans Flex

- Open source on Google Fonts since 2025-11-12 (Google Fonts metadata, checked
  Sep 2026). Axes: `wght` 1 to 1000, `wdth` 25 to 151, `opsz` 6 to 144,
  `slnt` -10 to 0, `GRAD` 0 to 100, `ROND` (roundness) 0 to 100. The shape
  page notes M3 shapes and Google Sans Flex "share roundness visual attributes."
- "Making Google Sans Flex" (design.google): Google Sans Flex is "a power tool
  for expression." Weight takes text from "calm as a whisper" to "loud and
  rugged"; roundness evokes a "personal, playful" tone and was the axis
  designers found most influential. Research with 3,000+ readers found taller,
  more elegant styles more premium and engaging. Optical size keeps expressive
  settings legible "from a smartwatch to a billboard," so it must track text
  size. Google Sans Code (open source, 2025) is the code face; Google Sans Mono
  was for medium and large editorial text and failed for code. Open-sourcing
  aims to close the visual gap between Google and third-party apps.
- It is the face most shipped Expressive apps use (observed in the reference
  screenshots below). Compose applies axes through
  `Font(resId, weight, variationSettings = FontVariation.Settings(...))` on
  API 26+; the resource overload is not experimental.

### Reference screenshots (patterns observed)

Sixteen screenshots of Google's Expressive showcases and third-party
Expressive apps (focus timer, music players, expense tracker, weather) show
recurring moves:

- Variable axes carry state: selected alarm times heavy in a filled container,
  unselected ones thin (same size).
- Tall condensed numerals (narrow width) filling cards; huge Display numbers
  for temperatures and totals.
- Mixed voices: mono for metadata and timecodes, serif for long reading, a
  bold display face for the hero.
- Metric cards: small label over a huge number, containers color-coded per
  category; an inverse (dark) card for the most important total.
- Shape-masked media and avatars (cookie, flower, clover), clustered avatars
  for groups, shapes as decorative badges.
- Pill hero controls (full-width Play or Pause with smaller round secondary
  buttons), connected button groups for options, wavy progress for playback
  and countdowns.
- Floating pill toolbars at the bottom with the active item as a filled pill.
- Segmented settings lists with icons in tonal circles and switches with
  check/close thumb icons.
- Color that follows content or state (weather sky), with seed color, palette
  style, and pure black dark mode offered as user settings.
- From the "Making Google Sans Flex" article images: two voices inside one
  line to encode meaning (origin light and condensed, destination heavy, wide,
  and slanted: "PDX › SFO"); type as graphic (oversized numbers and words
  cropped by the edge or tinted near their background); huge numerals with a
  small unit on the same baseline ("15 min", "0%"); big split action pairs
  (Snooze and Stop as half-width pills); arc text around circular progress.

### Editorial treatments

> "Standalone, showcase moments driven by type... type can freely dominate the
> screen."

- Three uses: **celebrate content** (a photo album cover), **voice of the
  user** (an enthusiastic message rendered huge), **bespoke functionality** (a
  light slider whose label widens and bolds as brightness rises).
- Match the emotion: narrow and thin for serenity, bold and italic for
  liveliness.
- Guard rails: tokenize treatments for consistency, match the emotional tone,
  don't mix clashing styles in one layout, don't mimic personalization
  theming, never on labels or purely informational text.
- Variable axes: weight (no very light weights at small sizes, no excessive
  weight at small sizes), grade (emphasis without reflow; negative grade in
  dark mode), width (narrow for tight labels; no wide type in app bars),
  optical size (match to type size).

## Motion

- The **motion physics system** (springs) replaces easing and duration for
  components. 21 Compose components use it by default; all component motion is
  driven by two tokens, expressive fast spatial and expressive fast effects.
- **Schemes:** **Expressive** ("Material's opinionated scheme... for most
  situations, particularly hero moments and key interactions," overshoots) and
  **Standard** ("more functional with minimal bounce... for utilitarian
  products"). Custom schemes are allowed.
- **Spec kinds:** **spatial** (position, rotation, size, corner radius;
  overshoots) and **effects** (color, opacity; no overshoot).
- **Speeds:** **fast** (small components like switches and buttons),
  **default** (partial-screen, like sheets and expanded rails), **slow** (full
  screen). Values resolve per device (watch vs phone vs tablet).
- Why springs: interruptible and retargetable with velocity carried through;
  they adapt across screen sizes because they're defined by damping and
  stiffness, not time.
- Three customization levels: preset scheme; custom `MotionScheme`; override
  the scheme per element via CompositionLocal (for example Standard for the
  app, Expressive for the hero). Shape morphing uses the Expressive scheme by
  default. The split button's menu rotation deliberately uses Standard.

```kotlin
MaterialExpressiveTheme(
    motionScheme = MotionScheme.expressive(), // or MotionScheme.standard()
) { /* ... */ }

// Custom components should use the theme's specs, not tween():
val scale by animateFloatAsState(
    targetValue = if (pressed) 1.1f else 1f,
    animationSpec = MaterialTheme.motionScheme.defaultSpatialSpec(),
)
val color by animateColorAsState(
    targetValue = if (pressed) activeColor else restColor,
    animationSpec = MaterialTheme.motionScheme.defaultEffectsSpec(),
)
```

Spring values from the androidx tokens (`ExpressiveMotionTokens.kt`,
`StandardMotionTokens.kt`), damping ratio / stiffness:

| Spec | Expressive | Standard |
| --- | --- | --- |
| Fast spatial | 0.6 / 800 | 0.9 / 1400 |
| Default spatial | 0.8 / 380 | 0.9 / 700 |
| Slow spatial | 0.8 / 200 | 0.9 / 300 |
| Fast effects | 1.0 / 3800 | 1.0 / 3800 |
| Default effects | 1.0 / 1600 | 1.0 / 1600 |
| Slow effects | 1.0 / 800 | 1.0 / 800 |

- **Plain `MaterialTheme` defaults to `MotionScheme.standard()`**; only
  `MaterialExpressiveTheme` defaults to expressive. This alone explains flat
  motion in stock code.
- `LocalMotionScheme` was removed (1.5.0-alpha27): override a subtree by
  nesting `MaterialTheme(motionScheme = ...)`.
- A custom scheme that snaps every spec immediately is Google's own pattern for
  a reduced-motion scheme ("mySnappyMotionScheme" in the I/O 2025 talk).
- The blog's "playful" custom scheme (spatial damping 0.6 with stiffness
  700 / 1400 / 300) is an example of customization, not the Expressive values.

Web approximations of the springs (a sanity check of the feel):

| Spring | Cubic bezier | Duration |
| --- | --- | --- |
| Expressive fast spatial | 0.42, 1.67, 0.21, 0.90 | 350ms |
| Expressive default spatial | 0.38, 1.21, 0.22, 1.00 | 500ms |
| Expressive slow spatial | 0.39, 1.29, 0.35, 0.98 | 650ms |
| Expressive fast / default / slow effects | 0.31, 0.94, 0.34, 1.00 / 0.34, 0.80, 0.34, 1.00 / 0.34, 0.88, 0.34, 1.00 | 150 / 200 / 300ms |
| Standard spatial (all speeds) | 0.27, 1.06, 0.18, 1.00 | 350 / 500 / 750ms |

Bounce belongs to components and hero moments; screen transitions stay simple
(see [Motion foundation](#motion-foundation-transitions)).

## Containment and spacing (Expressive)

- Give the most important content "visual prominence with generous spacing and
  the brightest surfaces." Negative space frames and emphasizes.
- Consistent placement of key actions builds recognizable focal points across
  screens.
- Group with containers; in lists and search results, **gaps** between filled
  items replace dividers.

## Components: what Expressive replaced

The most mechanical, highest-leverage part for the skill: stock code still
defaults to the left column.

| No longer recommended | Use instead |
| --- | --- |
| Medium and large top app bars | Medium flexible and large flexible app bars (shorter, larger title, subtitle, wrapping, left or center aligned) |
| Bottom app bar | Docked toolbar, or floating toolbar (horizontal or vertical, standard or vibrant, can pair with a FAB) |
| Segmented buttons | Connected button group |
| Navigation drawer | Expanded navigation rail (modal or standard) |
| Baseline navigation rail | Collapsed navigation rail |
| Baseline navigation bar | Flexible navigation bar (shorter; horizontal items in medium windows; active label uses `secondary`) |
| Small FAB, surface FABs | FAB, medium FAB, large FAB in primary / secondary / tertiary or their containers |
| Baseline extended FAB, surface extended FAB | Small (56dp), medium (80dp), large (96dp) extended FAB with larger type |
| Speed dial, stacked small FABs | FAB menu |
| Indeterminate circular progress for short waits | Loading indicator (shape morph, 200ms to 5s, contained or not; also pull-to-refresh) |
| Baseline list | Expressive list (standard or segmented, highlighted selection, slots) |
| Baseline menu | Vertical menu (standard or vibrant, gaps, submenus) |
| Search bar plus search view | Search (contained style recommended, grows wider when focused) |

New or expanded:

- **Buttons:** five sizes (XS, S, M, L, XL), round or square, shape morphs when
  pressed and when selected, toggle behavior. Small button padding 16dp.
- **Icon buttons:** five sizes (32, 40, 56, 96, 136dp), three widths, round or
  square, morph on press and select.
- **Button groups:** standard (pressed button changes shape and width, neighbors
  temporarily change width; selected toggles also change color) and connected.
  Selected buttons change round to square or square to round.
- **Split button:** menu half spins 180 degrees and changes shape when opened.
- **Progress indicators:** configurable track height and a **wavy** active
  track "for use cases that would benefit from increased expressiveness" (flat
  in very small buttons).
- **Sliders:** five sizes, vertical orientation, optional inset icon (Compose
  has no size presets; see the API reference).
- **Expressive lists:** the segmented style uses gaps between filled items;
  unselected items have 4dp inner and 16dp outer corners, selected items morph
  to 16dp all around. Swipe reveals mixed-style buttons, with the primary
  action last.
- **Expressive lists, more guidance:** people get circular or expressive-shape
  avatars, content gets square images; items with images can take a
  content-based container color; selection needs two cues, not color alone;
  lists can become cards or carousels at larger breakpoints.
- **Vertical menus:** vibrant style is tertiary-based. Group with gaps (not if
  the menu scrolls) or dividers. At most one or two gaps; disabled items stay
  visible; slots only for simple content; menus can become bottom sheets on
  compact screens.
- **Search:** container `surfaceContainerHigh`, never on `surfaceContainer`
  ("use surface container roles that are more than one step apart"); at most
  two trailing icons; hint text names what's searchable; 24dp margins
  unfocused, 12dp focused.
- **Toolbars:** "use the vibrant color style for greater emphasis" or to signal
  a temporary mode such as editing.
- **App bars:** can stay transparent on scroll with filled icon buttons
  floating over content; narrow icon buttons for Back.

## Restraint: collected cautions

Expressive is not "maximum everything." Every source pairs a push with a limit:

- One or two hero moments per product.
- Don't combine too many tactics at once; secondary screens use fewer.
- One focal point per screen; not every element large.
- Don't use the same color role for all actions and data.
- Abstract shapes sparingly and only with a reason; not on text-heavy
  containers.
- Uniform shapes for grouped data; no overlapping shapes.
- One moving thing at a time in a hero moment.
- Vibrant components sparingly; filled buttons sparingly.
- Mixed button sizes in a group only for hero moments.
- Transitions stay simple.
- Editorial type never on labels or purely informational text.
- Respect reduced motion (fades, no morphing or parallax).
- A strong minority prefers calmer designs; function beats flourish.

### Customizing without forking

From "Make Material your own" (I/O 2026): "Material is no longer an 'all or
nothing' choice." Brand identity sits on top of Material's research-driven,
accessible UX layer.

- Order of preference: **use as-is** (easiest), **wrap** (more control, but you
  own the API and forward new features), **Styles API** for visual changes
  (coming to Material components; can set a pressed background, even a
  gradient, without tracking interaction state), **fork** only as a last
  resort. "When you fork, you break your connection to the Material
  foundation": accessibility, behavior, and updates are lost "all for the sake
  of a stylistic change."
- Google's brand examples keep Material behavior and add one subtle touch:
  Shrine adds a custom shadow and animated background to a single promo button
  (keeping its shape morph); Achieve replaces the ripple with a custom reward
  animation on task completion, because rewarding the user is core to the
  brand; Clip uses "oversized graphic components" for a bold, distinct look.


## Compose API reference (verified September 2026)

Verified against androidx-main (`bf95ca5`, Sep 22, 2026) and the released
`material3-android-1.5.0-alpha28` sources, whose public API matches main
except `Slider`. Source root:
`compose/material3/material3/src/commonMain/kotlin/androidx/compose/material3/`.

### Versions and stability

- `androidx.compose.material3:material3`: stable **1.4.0** (Sep 2025) has
  **no Expressive APIs** except `WideNavigationRail`, `ModalWideNavigationRail`,
  and `ShortNavigationBar`. Expressive requires **1.5.0-alpha28** (Sep 9, 2026);
  1.5.0 has no beta yet.
- Within 1.5.0 alphas nearly everything was promoted out of experimental:
  motion (alpha15), typography (alpha16), `MaterialExpressiveTheme` and
  wavy progress (alpha18), buttons, toggles, menus, FABs (alpha19), split
  button (alpha20), floating toolbar and button groups (alpha22), flexible app
  bars, expressive list items, contained search (alpha23).
- **Still `@ExperimentalMaterial3ExpressiveApi`:** `MaterialShapes`,
  `RoundedPolygon.toShape()` / `toPath()`, `Morph.toPath()`, `LoadingIndicator`,
  `ContainedLoadingIndicator`, and the pull-to-refresh loading indicator.
  **Still `@ExperimentalMaterial3Api`:** `AppBarWithSearch`,
  `carouselParallaxScrollEffect`, sheets, tooltips.
- Other versions: adaptive 1.3.0 stable (1.4.0-alpha02); Wear compose-material3
  1.6.2 stable (1.7.0-rc01); graphics-shapes 1.1.0; MDC Views 1.14.0.
- **Replaced components are not deprecated.** Lint won't flag medium/large top
  app bars, the bottom app bar, or segmented buttons, so the skill has to steer.
  Exception: `ListItem(headlineContent = ...)` is deprecated.

### Theme

```kotlin
@Composable fun MaterialExpressiveTheme(
    colorScheme: ColorScheme? = null, motionScheme: MotionScheme? = null,
    shapes: Shapes? = null, typography: Typography? = null, content: @Composable () -> Unit,
)
```

- Top-level nulls default to `expressiveLightColorScheme()`,
  `MotionScheme.expressive()`, `Shapes()`, `Typography()`; nested nulls inherit.
- `expressiveLightColorScheme()` is `lightColorScheme` with on*Container roles
  at tone 30. **There is no `expressiveDarkColorScheme`**; use
  `darkColorScheme()`.
- `MotionScheme` specs: `default/fast/slowSpatialSpec<T>()` and
  `default/fast/slowEffectsSpec<T>()`, read from `MaterialTheme.motionScheme`.

```kotlin
@Composable fun AppTheme(dark: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit) {
    val context = LocalContext.current
    val scheme = when {
        Build.VERSION.SDK_INT >= 31 -> if (dark) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        dark -> darkColorScheme()
        else -> expressiveLightColorScheme()
    }
    MaterialExpressiveTheme(colorScheme = scheme, motionScheme = MotionScheme.expressive(), content = content)
}
```

### Color schemes from a seed

- Compose has **no seed or variant API**: only `dynamicLight/DarkColorScheme(context)`
  (API 31+), with no style parameter.
- Variants (TonalSpot, Neutral, Vibrant, Expressive, Rainbow, FruitSalad,
  Monochrome, Fidelity, Content) live in material-color-utilities, which has no
  Maven artifact; MDC's copy is `@RestrictTo`.
- Practical options: export a static brand scheme from Material Theme Builder;
  or generate at runtime with the third-party **MaterialKolor**
  (`com.materialkolor:material-kolor:5.0.1`):
  `rememberDynamicColorScheme(seedColor, isDark, specVersion = ColorSpec.SpecVersion.SPEC_2025, style = PaletteStyle.Expressive)`.
- The videos add: dynamic color on recent Android versions has "more variation
  and higher chroma across all hues," and Google "strongly encourage[s]" it.

### Shapes

- `Shapes(extraSmall, small, medium, large, extraLarge, largeIncreased,
  extraLargeIncreased, extraExtraLarge)`: 4, 8, 12, 16, 28, 20, 32, 48dp; read
  as `MaterialTheme.shapes.largeIncreased` and so on (1.5.0 only).
- `MaterialShapes` (experimental), 35 `RoundedPolygon`s: Circle, Square,
  Slanted, Arch, Fan, Arrow, SemiCircle, Oval, Pill, Triangle, Diamond,
  ClamShell, Pentagon, Gem, Sunny, VerySunny, Cookie4Sided, Cookie6Sided,
  Cookie7Sided, Cookie9Sided, Cookie12Sided, Ghostish, Clover4Leaf,
  Clover8Leaf, Burst, SoftBurst, Boom, SoftBoom, Flower, Puffy, PuffyDiamond,
  PixelCircle, PixelTriangle, Bun, Heart.
- `RoundedPolygon.toShape(startAngle = 0): Shape` (composable) for clipping;
  `Morph(start, end)` from `androidx.graphics.shapes` plus
  `Morph.toPath(progress)` (unit space) for morphing. Prefer components'
  built-in `shapes` / animated shapes over hand-built morphs, which "usually
  require many lines of code."

```kotlin
@OptIn(ExperimentalMaterial3ExpressiveApi::class)
private class MorphShape(val morph: Morph, val progress: Float) : Shape {
    override fun createOutline(size: Size, layoutDirection: LayoutDirection, density: Density): Outline {
        val path = morph.toPath(progress = progress)
        path.transform(Matrix().apply { scale(size.width, size.height) })
        return Outline.Generic(path)
    }
}
// val morph = remember { Morph(MaterialShapes.Circle, MaterialShapes.Cookie9Sided) }
// val t by animateFloatAsState(if (selected) 1f else 0f, MaterialTheme.motionScheme.defaultSpatialSpec())
// Modifier.clip(MorphShape(morph, t))
```

### Typography

- 15 `*Emphasized` properties on `Typography` (`displayLargeEmphasized` to
  `labelSmallEmphasized`), 1.5.0 only. Emphasized weights: Display, Headline,
  Body, and Title Large go Regular to Medium; Title Medium/Small and Labels go
  Medium to Bold.
- New `Typography(fontFamily = ...)` constructor sets one family for all styles.

### Spacing

- **No spacing tokens in Compose** (no `space100`). Define an app spacing object
  on the 8dp scale.

### Components

| Guideline name | Compose API | Notes |
| --- | --- | --- |
| Medium / large flexible app bar | `MediumFlexibleTopAppBar`, `LargeFlexibleTopAppBar` | `subtitle`, `titleHorizontalAlignment`; pair with `TopAppBarDefaults.exitUntilCollapsedScrollBehavior()` |
| Search app bar | `AppBarWithSearch` | Experimental; nav and action slots outside the field |
| Contained search | `rememberContainedSearchBarState()`, `SearchBarDefaults.containedColors(state)`, `ExpandedFullScreenContainedSearchBar` | `SearchBar(state, inputField)` is stable |
| Floating toolbar | `HorizontalFloatingToolbar`, `VerticalFloatingToolbar` | FAB overload; `FloatingToolbarDefaults.vibrantFloatingToolbarColors()`, `VibrantFloatingActionButton`, `exitAlwaysScrollBehavior`, `floatingToolbarVerticalNestedScroll`, `ScreenOffset` |
| Docked toolbar | `FlexibleBottomAppBar` | No "DockedToolbar" composable |
| FAB menu | `FloatingActionButtonMenu`, `FloatingActionButtonMenuItem`, `ToggleFloatingActionButton` | `Modifier.animateIcon`, `animateFloatingActionButton(visible, alignment)` |
| FAB sizes | `MediumFloatingActionButton`, `LargeFloatingActionButton`, `Small/Medium/LargeExtendedFloatingActionButton` | |
| Buttons XS to XL | `Button(onClick, shapes = ButtonDefaults.shapesFor(h))` | Heights `ExtraSmallContainerHeight` 32, `MinHeight` 40, `MediumContainerHeight` 56, `LargeContainerHeight` 96, `ExtraLargeContainerHeight` 136; `contentPaddingFor`, `iconSizeFor`, `textStyleFor` |
| Toggle button | `ToggleButton` (+ Elevated, FilledTonal, Outlined) | `ToggleButtonSize.ExtraSmall..ExtraLarge` |
| Standard button group | `ButtonGroup(overflowIndicator, ...)` | Scope: `clickableItem`, `toggleableItem`, `customItem`, `Modifier.animateWidth(interactionSource)`; overflows automatically |
| Connected button group | `Row` of `ToggleButton`s | `ButtonGroupDefaults.ConnectedSpaceBetween`, `connectedLeading/Middle/TrailingButtonShapes()` |
| Split button | `SplitButtonLayout` | `SplitButtonDefaults.LeadingButton`, `TrailingButton(checked, onCheckedChange)` |
| Icon button sizes | `IconButton(shapes = ...)` + `Modifier.size(IconButtonDefaults.mediumContainerSize(IconButtonWidthOption.Wide))` | 32/40/56/96/136dp; Narrow, Uniform, Wide; round, square, pressed shapes |
| Loading indicator | `LoadingIndicator`, `ContainedLoadingIndicator` | Experimental |
| Wavy progress | `LinearWavyProgressIndicator`, `CircularWavyProgressIndicator` | `amplitude`, `wavelength`, `waveSpeed` |
| Expressive list | `ListItem(onClick, ...) { headline }`, `SegmentedListItem(shapes = ListItemDefaults.segmentedShapes(i, n))` | Space with `ListItemDefaults.SegmentedGap` (2dp) |
| Vertical menu | `DropdownMenuPopup`, `DropdownMenuGroup(shapes = MenuDefaults.groupShape(i, n))`, `SelectableDropdownMenuItem`, `CheckableDropdownMenuItem` | Vibrant: `MenuDefaults.itemVibrantColors()`; submenus by nesting `DropdownMenuPopup` (the site says submenus aren't in Compose; the source disagrees) |
| Flexible navigation bar | `ShortNavigationBar`, `ShortNavigationBarItem(iconPosition = Top or Start)` | Stable since 1.4.0 |
| Collapsed / expanded rail | `WideNavigationRail(state = rememberWideNavigationRailState())`, `ModalWideNavigationRail` | Stable since 1.4.0; `NavigationSuiteScaffold` adapts bar and rail |
| Slider | `Slider(state = s, onValueChange = { ... })`, `VerticalSlider` | **No XS to XL size presets**; inset icons are custom-drawn |
| Carousel | `HorizontalMultiBrowseCarousel`, `HorizontalUncontainedCarousel`, `HorizontalCenteredHeroCarousel` | Stable |

```kotlin
// Flexible app bar
val scroll = TopAppBarDefaults.exitUntilCollapsedScrollBehavior()
Scaffold(
    Modifier.nestedScroll(scroll.nestedScrollConnection),
    topBar = { LargeFlexibleTopAppBar(title = { Text("Inbox") }, subtitle = { Text("3 unread") }, scrollBehavior = scroll) },
) { }

// Sized morphing button
val h = ButtonDefaults.MediumContainerHeight
Button(onClick = {}, shapes = ButtonDefaults.shapesFor(h), modifier = Modifier.heightIn(h),
    contentPadding = ButtonDefaults.contentPaddingFor(h)) { Text("Start", style = ButtonDefaults.textStyleFor(h)) }

// Connected button group
Row(horizontalArrangement = Arrangement.spacedBy(ButtonGroupDefaults.ConnectedSpaceBetween)) {
    options.forEachIndexed { i, label ->
        ToggleButton(
            checked = selected == i, onCheckedChange = { selected = i },
            shapes = when (i) {
                0 -> ButtonGroupDefaults.connectedLeadingButtonShapes()
                options.lastIndex -> ButtonGroupDefaults.connectedTrailingButtonShapes()
                else -> ButtonGroupDefaults.connectedMiddleButtonShapes()
            },
            modifier = Modifier.weight(1f).semantics { role = Role.RadioButton },
        ) { Text(label) }
    }
}

// Segmented list
Column(verticalArrangement = Arrangement.spacedBy(ListItemDefaults.SegmentedGap)) {
    items.forEachIndexed { i, item ->
        SegmentedListItem(onClick = {}, shapes = ListItemDefaults.segmentedShapes(i, items.size)) { Text(item) }
    }
}
```

### Other APIs mentioned in the talks

- Navigation 3 list-detail: `ListDetailSceneStrategy` with entries tagged
  `ListDetailScene.ListPane` / `DetailPane`; predictive back comes free.
- Adaptive: `ExperimentalMediaQueryApi` for posture, Grid and FlexBox APIs.
- Styles API integration for Material components is announced but in progress.

## Open questions

1. `Material3ExpressiveApi` (a no-opt-in marker mentioned in alpha18 notes)
   doesn't exist on main; whether it shipped and was removed is unverified.
2. Material Theme Builder's current Compose export format (static brand scheme
   path) is unverified.
