# Android UI design guides

Research from Android's own design guidance at
[developer.android.com/design/ui](https://developer.android.com/design/ui), the
platform layer that sits beside Material ([material-3.md](material-3.md)).
Material says how components and styles work; these guides say how an Android
app behaves on the platform: system bars, edge-to-edge, adaptive layouts,
settings, onboarding, notifications, widgets, and desktop input. Wear OS
findings live in [material-3-wear-os.md](material-3-wear-os.md).

## Sources and status

Fetched September 2026: Mobile guides (foundations, styles, layout and content,
patterns, home screen, widgets), the widget hub and the widget quality
guidelines (`/docs/quality-guidelines/widget-quality`), Desktop guides, the
large screens hub, and the 41-page Gallery of large-screen case studies. Cars,
TV, XR, and AI glasses were skipped as out of scope.

## What this adds to the Material research

Material covers what a screen looks like; these guides cover platform
behavior Material leaves out. The most useful additions for a design skill:

1. **Android conventions that generated code often gets wrong**, especially
   iOS habits (see [Translating from iOS](#translating-from-ios)).
2. **Platform surfaces with their own rules:** widgets, notifications, live
   updates, picture-in-picture.
3. **Concrete pattern guidance:** settings, onboarding, sign-in, help,
   predictive back.
4. **Desktop and input:** hover, right-click, keyboard, cursors, density.

## Color and theming (platform view)

- "An oversaturated look can result in using only the base color roles of
  primary, secondary, or tertiary. To help with your color hierarchy, apply
  color schemes to include less vibrant container colors and outline roles."
- Use a vibrant primary for the most prominent action; a FAB in a muted tone
  matching the navigation blends in.
- "Apps can be vibrant and expressive, but stick to a palette of colors. Too
  many semantic colors can be confusing, too many decorative colors
  overwhelming."
- Keep semantic colors consistent: if purple means membership, it always means
  membership.
- Surfaces: "Don't be shy to use lots of surface space; the human eye needs
  space to relax."
- Content color: "avoid pulling colors from multiple content pieces"; works best
  with one primary content source.
- HCT hue limits: yellow has little chroma at dark tones; blue peaks at dark
  tones; light blue and bright light red are physically hard to get.
- A primary color can be the brand color, or a separate interactive color so
  brand colors are used more sparingly.
- Always ship a static fallback scheme; without one the system falls back to
  the baseline purple.
- Themes: respect system light and dark, dynamic color, contrast, and font
  size; offering an in-app override is fine; don't lock the app to light theme.
  Use one primary theme source for most of the UI.
- Design systems can extend Material with their own systems (gradients,
  spacing) as additional theme values.
- Use one icon style (outlined, rounded, sharp) across the app.

## Accessibility (platform minimums)

- Body text never below 12sp; always `sp` for text.
- 4.5:1 text contrast; 3:1 between surfaces and non-text elements such as icons.
- Never make color the only affordance; links and actions need a second cue.
- 48dp touch targets even when the visual is smaller.
- Don't rely on gestures alone: every swipe action needs a visible alternative
  (a trash icon beside swipe-to-delete) and an accessibility action.
- Decorative images get a null description; group related elements so screen
  readers can skip between blocks.

## System bars and edge-to-edge

- Draw backgrounds and scrolling content behind the status and navigation bars
  (enforced from Android 15; call `enableEdgeToEdge()`).
- Keep the gesture navigation bar transparent; never add a background to it.
  Three-button navigation gets a translucent scrim when content scrolls under
  it.
- Status bar: transparent when nothing scrolls under it or an image sits there;
  translucent (gradient protection) when content scrolls beneath. Material's
  `TopAppBar` already protects; don't stack protections. In multi-pane layouts,
  each pane gets protection matching its own background.
- Small top app bars collapse to status bar height or scroll off; flexible
  bars compress.
- No tap or drag targets inside system gesture insets.
- Backgrounds, dividers, and carousels draw edge-to-edge (carousels even into
  display cutouts); text and buttons are inset with display-cutout insets.
- Immersive mode (video, reading, games): hide bars only when the benefit
  exceeds "extra space," let a tap reveal controls and bars, and put a scrim
  under overlaid text.
- Sync UI with the keyboard using inset animations; pin text inputs to the
  keyboard rather than letting it hide them.

## Layout

- **Adaptive is the default**, not an afterthought. Never lock to portrait
  (letterboxing when resized).
- **Set max widths:** don't stretch content, buttons, inputs, or tabs full
  width on large screens; change presentation instead (bottom sheet becomes a
  side sheet, list becomes cards or a carousel, extended FAB appears).
- **Think in panes and containment**, not screens: compact one pane, medium one
  or two, large multiple.
- Adapt at the component or pane level with the media query API rather than
  designing whole screens per size, input, and posture.
- Compact margins 16dp; mobile uses a 4-column grid. Choose the grid by content:
  **hierarchical** (editorial, detail screens), **modular** (equal items like a
  gallery), **column** (flexible one-direction flow); manuscript and masonry
  exist too. Break the grid only when content needs it.
- Baseline grid: 8dp for layout and components, 4dp for icons, type, and small
  elements.
- Recommended aspect ratios: 16:9, 3:2, 4:3, 1:1, 3:4, 2:3. Notate how images
  scale and crop per breakpoint (fixed ratio, changing ratio, or fixed height).
- Consistent spacing between like elements; inconsistent spacing "appears
  haphazard." Don't show too many actions per view.
- Pin critical actions (FAB) and inputs (message field) rather than letting
  them scroll away.
- **Navigation pairings:** navigation bar (3 to 5 destinations) or modal drawer
  for primary; tabs or a bottom toolbar for secondary. Don't stretch a bottom
  nav bar on large screens; switch to a rail. A rail can be more ergonomic even
  on a compact cover screen.
- **Actions:** one FAB for the most important action; secondary actions in the
  top bar or next to their content; rare actions in an overflow menu.
- **Landscape and postures:** landscape phones are medium width with compact
  height (horizontal nav bar items or a rail); tabletop posture suits large
  controls and landscape video; keep controls and text out of the hinge (wider
  gutter); cover screens stay focused, with hero art as the background and a
  big anchor control like play.
- If content didn't scroll in portrait, don't make it scroll in landscape.

## Translating from iOS

The "10 Steps to Android" guide lists the iOS habits to remove. Useful as an
anti-pattern list for generated UI:

| iOS habit | Android |
| --- | --- |
| Tab bar | Navigation bar (3 to 5), secondary items move to the top app bar or a FAB |
| Centered nav bar title | Title left-aligned by default; large titles become large flexible app bars that collapse on scroll |
| Back chevron | Up arrow for hierarchy; system back and predictive back handle "back" |
| "Cancel" / "Done" text buttons | Icons (close) for dismissal; full-screen dialog app bar for modal tasks |
| Action sheets, share sheets | Bottom sheets |
| Segmented control for sibling views | Tabs (swipeable); for option selection, Material's connected button group |
| Table cells with dividers and disclosure chevrons | Lists with dividers used sparingly and no disclosure indicators |
| iOS toggles and pickers | Material switches, checkboxes, radio buttons, text fields |
| SF Symbols, San Francisco | Material Symbols; Roboto or a brand font |
| iOS motion | Material motion: ripple, container transform, shared axis, fade through |

Design frames at 412dp wide and test at 360dp.

## Patterns

### Settings

- Respect system settings; the app may not need its own. Never replicate or
  override device settings (they can be accessibility needs); extend them
  instead (for example more granular theming).
- Include infrequent preferences only; frequent actions live next to their
  feature (captions settings on the video player).
- **Don't put** app info (version, licenses) or account management on the
  settings screen; give them their own destinations.
- Defaults: what most users would pick, low risk, low battery and data, only
  interrupt when important.
- Placement: usually secondary navigation (top bar icon or overflow, after
  everything except Help & Feedback); call it "Settings," never "Options" or
  "Preferences"; accessible when signed out.
- Layout: an overview list of the most important settings with values shown;
  grouped with containment and headings; 15 or more settings means subscreens;
  subscreen title matches the label that opened it; add search for deep
  hierarchies. On large screens, list-detail with the overview as the list;
  never stretch single-pane items full width.
- Controls: switch or checkbox for on/off (never a single radio button); radio
  buttons in a dialog or child screen for one-of-many; slider for ranges or
  imprecise values; dependent settings sit under their parent with a reason
  when disabled.
- Labels: most important words first; neutral terms ("Block," not "Don't");
  impersonal ("Notifications," not "Notify me"); no generic verbs (Set, Change,
  Edit, Manage, Use, Select, Choose); don't repeat the section title. Supporting
  text shows status, not a description of the setting.

### Onboarding and sign-in

- Separate what must happen before using the app from what can happen in
  context. Show value before asking for permissions or an account; prime
  permissions at the moment of need.
- Prefer previews of real content over interstitial slides; always offer skip,
  and let people resume later.
- Show progress with steppers or progress indicators, never with decoration
  that could be mistaken for progress.
- Collect minimum data; group related fields; avoid long scrolling forms and
  one-input-per-screen overkill; state password rules; recovery ("Forgot
  password") is always easy to find.
- Passkeys through Credential Manager as the default; don't list every sign-in
  method as separate buttons. Button label "Create a passkey"; "Sign in," not
  "Log in."
- Snackbars for minor confirmations; error copy that focuses on the fix, never
  mocks.
- On expanded layouts, cap form width; never stretch buttons and inputs.

### Help and feedback

Secondary navigation (overflow, bottom of a drawer, settings). Standard labels
"Help" and "Send feedback." Most common scenarios first, legal pages last.

### Predictive back

- Full-screen surfaces: exit scales 100% to 90%, enter 110% to 100%, fade
  through at 35% progress, interpolator (0.1, 0.1, 0, 1).
- Shared-element back preview: surface detaches, x shift ((screen width / 20) -
  8)dp, y shift ((height / 20) - 8)dp, scale down to 90%, 8dp margin; feed
  gesture progress through a standard decelerate curve; a fling commits after
  briefly reaching the max preview; cancel springs back.
- Don't suggest the item is being dismissed in the gesture's direction.

## Home screen surfaces

### Widgets

- One primary use case per widget; separate widgets for distinct jobs
  (a steps widget and a streak widget), not one widget per color variant.
- **Canonical layouts** (Jetpack Glance samples exist for each): text only,
  text and image, search toolbar, toolbar, text and image list, checklist,
  action list, full-bleed image (snap scroll, Android 17+), image grid, image
  and text grid.
- **Fill the bounds:** misalignment is a main reason users remove widgets.
  Rectangular widgets touch all four grid edges; custom shapes touch at least
  two opposing edges (all four for differentiated quality). No custom padding.
- **Style:** Material color roles and dynamic themes; light and dark; the
  system corner radius for rectangular widgets. Minimal-content widgets
  (photo, weather, now playing) can take a whole expressive shape; data-heavy
  widgets use expressive shapes for hierarchy or the call to action. "Get
  bolder with headlines, labels, and data" through dramatic type scales.
- **Sizes (handheld, dp):** 2x1 109-306 by 56-130; 2x2 109-306 by 115-276; 2x3
  109-306 by 185-422; 4x1 245-624 by 56-130; 4x2 245-624 by 115-276; 4x3
  245-624 by 185-422. Tablets have their own table. Use breakpoints to add or
  remove content as the widget resizes; add 48dp buttons when room allows.
- **Configuration:** open it on placement only if the widget is empty without
  it; otherwise ship a good default and configure later. One or two screens,
  preview the result, progressive disclosure, no dead ends.
- **Picker:** accurate previews at the real size (and in dynamic colors), a
  clear description, 6 to 8 variations at most. Promote pinning in-app at
  relevant moments, subtly, never blocking the main task.
- **Quality tiers** (`/docs/quality-guidelines/widget-quality`):
  - Low quality: doesn't fill bounds, poor contrast, no name in the design, no
    preview, stale content, doesn't update after actions, cropped content.
  - Standard: aligned, sensible min and max sizes (min still useful with 48dp
    targets), accurate previews, intentional empty and signed-out states,
    manual refresh when data outpaces the UI.
  - Differentiated: fills all four edges, resizes to 2x2, 4x1, or 4x2, a
    consistent header (icon always, title when space allows), device or app
    color theming, light and dark, previews with user content or system theme,
    unique name and description, system corner radius, loading state spec,
    system configuration entry, system launch transition.

### Notifications

- Only for timely, relevant value: never ads, "we miss you," rating requests,
  holiday greetings, silent syncing, or self-recovering errors.
- Title under 30 characters without the app name; body under 40.
- Large icon only when it reinforces content (sender's photo is circular;
  other images square), never for branding.
- Up to three actions; don't duplicate the tap-on-body action; offer inline
  replies for short text.
- Pick the template (standard, big text, big picture, progress with cancel,
  media, messaging, call), set channels and importance honestly, group bursts,
  mark sensitivity for the lock screen, and dismiss stale ones.
- Ask for notification permission after explaining the benefit in context.

### Live updates and picture-in-picture

- Live updates: only for finite, user-initiated, trackable activities (ride,
  delivery); alert only on critical changes; progress that matches reality;
  the same timestamp format in the status chip and the card.
- PiP: video (and maps) only, nothing but the content in the window, auto-enter
  on home, smooth transitions with a source rect hint; a new selection plays in
  the existing player.

## Desktop and large screens

- **Principles:** adaptive from the start; "more screen, more done" (denser,
  not bigger); faster motion for small nearby elements, slower or simpler for
  large movements; multitasking inside the app; balance efficiency with
  simplicity; touch, pointer, and keyboard are equally important.
- **Scale:** type a step or two up for viewing distance; max widths on content
  and components; about 60 characters for short copy; progressive disclosure
  as windows grow.
- **Navigation:** rail instead of a stretched bottom bar; tabs at a max width;
  app bars can attach to their pane.
- **Density:** denser data is fine with precise input; tighter click targets
  (pointer targets can go below 48dp, for example secondary hover actions; no
  oversized intrinsic targets).
- **Pointer:** every primary journey works with left click alone; right-click
  opens context menus (not long press); hover shows state, tooltips, and
  secondary actions (checkboxes on hover for selection); instant
  click-and-drag; cursor icons communicate what's possible.
- **Keyboard:** Tab moves in reading order, arrows move within components,
  Escape dismisses temporary UI, initial focus on the key element (search, the
  primary action), visible focus styles, standard shortcuts (Enter sends, Space
  plays) and the Keyboard Shortcuts Helper.
- **Windows:** multi-instance, drag and drop between apps, PiP for playback, a
  customizable window header bar (navigation or search in its middle).

### Gallery patterns by app category

| Category | Large-screen pattern |
| --- | --- |
| Media | Details, reviews, and related titles beside the player; browse while playing; tabletop posture for lean-back viewing; PiP |
| Reading | Two-page spread on foldables; adapted line length; collapsible pane for notes and comments; full-screen reading |
| Shopping | Filters in a supporting pane; detail beside the product list; drag-and-drop cart and wish list |
| Social | Conversations list-detail; comments in a supporting pane; drag and drop to share; Enter sends |
| Productivity | Tabletop layouts for calls; tools and comments without covering the document; file thumbnails with right-click menus |
| Creativity | Movable, resizable palettes; contextual menus; stylus hover, tilt, pressure, palm rejection |

Case studies report real gains (Concepts: 70% more time in app on tablets;
eBay and Google Duo rating improvements), which argues for treating large
screens as a first-class target.
