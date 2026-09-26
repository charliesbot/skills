# Material 3 Expressive for Wear OS

Design research for Material 3 Expressive on watches. It extends
[material-3.md](material-3.md): the phone guidance on emphasis, hero moments,
color roles, and springs still applies, and this document records what changes
on a round, glanceable, battery-bound screen.

Implementation (Compose for Wear OS APIs, `AppScaffold`, `ScreenScaffold`,
`TransformingLazyColumn`, migration) is covered by the installed
`wear-compose-m3` skill. This document is about design judgment.

## Sources and status

- **developer.android.com Wear OS design guide** (`/design/ui/wear/guides/`),
  41 current pages, fetched September 2026: get started, foundations (adaptive
  design, quality tiers, common layouts), styles (color, typography), surfaces
  (apps, tiles), patterns (gestures, media).
- **m3.material.io Design for watches** (Overview, Foundations, Styles,
  Layout tabs), content version `2026-09-16_06-10-03`.
- **Gaps:** the M3 design guide has no component or motion pages yet (APIs and spring values below come from androidx sources); component URLs
  redirect to the older Material 2.5 guide. Its behavior and surface pages
  (navigation, physical buttons, launch, sign-in, watch faces) were read later
  and still describe platform behavior; its component styling was skipped. Shape details
  (edge-hugging button specs, corner values) and Wear spring values come from
  the Compose APIs, not the design guide.

## What Expressive means on a watch

> "Material 3 Expressive is the latest design system for our smallest screen:
> the watch... emphasizes premium and a greater level of expression."

Five principles:

1. **Embrace round.** A shape framework for round screens that uses the whole
   canvas. **Edge-hugging buttons** and containers are "an ownable and iconic
   design pattern for round devices."
2. **Premium motion and springs.** Motion spatially connects surfaces and
   responds to gestures.
3. **Shape morphing.** Lists and selectable buttons morph their corners;
   containers round and sharpen to show state.
4. **Rich color.** Deeper tonal palettes, a third accent color, and more
   specific roles ("dim" variants, containers inside accent groups).
5. **Variable fonts.** Roboto Flex replaces Roboto everywhere, with a type
   scale tailored to the round screen.

### Levels of expression

Google grades Wear apps on three tiers and asks for **excellent or
transformative**:

| | Foundational | Excellent (required) | Transformative (recommended) |
| --- | --- | --- | --- |
| Components | Baseline migration | Multiple expressive components | Multiple expressive components and customization |
| Color | Baseline palette | Dynamic palette themes | Dynamic themes and/or unexpected color combinations |
| Typography | Roboto Flex | Roboto Flex | Roboto Flex with morphing |
| Shape | None | Some shape library and containment | Shape library and expressive containment |
| Motion | Motion tokens | Tokens plus some expressive motion (shape morphing, springs) | Same |
| Hero moments | None | Product-specific expressive moments | Expressive moments plus dramatic hierarchy and customization |
| Adaptive | Percentage margins | Plus added value after the 225dp breakpoint | Plus large-screen-specific designs |

This table is a useful self-check for generated Wear screens: "unremarkable"
is an explicit failure ("Don't let your app or tile be unremarkable").

## Watch design principles

- **Critical tasks only:** focus on one or two tasks, not a full app. Help
  people finish within seconds (arm fatigue); tiles get about 7 seconds of
  attention.
- **Glanceable:** test while moving and distracted. No spreadsheets, calendar
  grids, or dense data.
- **Shallow and linear:** no hierarchy deeper than two levels; content and
  navigation inline.
- **Vertical only:** scroll in one direction.
- **Always relevant:** adapt to time, place, activity. **Works offline.**
- Round screens have 22% less space than square ones and need larger margins.

## Color

- **Build from black.** Watch UIs sit on a black background (battery, and it
  makes the bezel disappear). Tiles must never use a full-bleed image or color
  background.
- Dynamic theming derives from **two seed colors** (primary and tertiary) set by
  the watch face, and is WCAG AAA. A brand color can seed a custom theme
  instead. Media apps take their seed from the artwork, falling back to the
  watch face theme or a monochrome palette.
- Roles: three accent groups (primary, secondary, tertiary) each with
  **base, dim, and container** variants, plus error (error, errorDim,
  errorContainer) and surfaces (surfaceContainerLow, surfaceContainer,
  surfaceContainerHigh).
  - **Primary:** the most important actions, edge-hugging buttons, active
    states. **PrimaryDim:** visually distinct but not demanding attention.
    **PrimaryContainer:** cards or selected states that highlight content.
  - **Secondary:** supporting actions in dense UI. **Tertiary:** "drawing
    attention to key elements," standout feedback, a goal being reached, tap
    responses.
  - **Error:** delete and dismiss actions (like swipe to reveal).
    **ErrorDim:** high-priority emergencies and stop buttons.
  - Semantic color matters more on a watch: red for errors, green for success.
- Only pair `X` with `onX` (7:1 on watch).

Recommended pairings:

| Pairing | Use |
| --- | --- |
| Primary + PrimaryDim | Main action plus complementary items |
| PrimaryDim + Tertiary | Highlight important elements, tertiary for standout feedback (tap responses) |
| Primary + SecondaryContainer | Key elements stand out, less prominent content recedes |
| Primary + PrimaryContainer | Main action plus complementary items with depth |
| PrimaryDim + TertiaryDim | Highlight plus feedback such as a goal met |
| Tertiary + Primary + SecondaryContainer | When there's no single main action |
| Secondary + PrimaryContainer | Two equally important options that still contrast |
| Primary + TertiaryDim | Main action plus a complementary accent |

Tiles: primary colors on primary actions, secondary or tertiary on the rest;
never all filled primary buttons.

## Typography

- **Roboto Flex** with two axes that matter most: **weight** (no very light
  weights for small text; no excessive weight at small sizes) and **width**
  (narrow fits long names and numbers; no wide type in page headers).
- **Variable axes in motion:** animate weight, width, or both as expressive
  feedback.
- 21 styles in six roles:
  - **Display** (L/M/S): short, highly glanceable hero information, key
    metrics, brand moments. Doesn't scale with the user font setting.
  - **Title** (L/M/S): wayfinding (page and section titles), not interactive
    components.
  - **Label** (L/M/S): text inside components; LabelMedium is the most common.
  - **Body** (L/M/S/ExtraSmall): content text, timestamps, metadata.
  - **Numeral** (ExtraLarge to ExtraSmall): a few digits that need no
    localization (charging screen, timer, step count, pickers, workout
    metrics); tabular by default and can take expressive width.
  - **Arc** (L/M/S): curved text at the top or bottom edge (time text, page
    titles, confirmation overlays, calls to action), with spacing tuned for the
    curve.
- Line height about **1.1x** on watch (vs 1.2x on phone); Compose adds extra
  line height to the last line.
- User font scaling moves in 6% steps; nothing 20sp and up scales. Design for
  the largest and smallest settings.
- Tabular numbers for anything that animates or changes.

## Shape

- Edge-hugging buttons at the bottom of scrolling lists and non-scrolling
  screens use the curve instead of fighting it; bezel-hugging elements
  (progress rings, scroll indicators, time text) grow automatically with the
  screen.
- Flexible containers round and sharpen corners to show state; lists and
  selectable buttons morph.
- Grouped containers distribute space evenly for symmetry, or unevenly to
  establish hierarchy and emphasize the important item.
- Loading animations, button groups, and layouts use the shape library.

## Layout

- **Structure:** time text (recommended on app screens, but not on dialogs,
  confirmation overlays, or pickers) and optional title at the top; content in
  the middle; action buttons at the bottom (an end-of-list **edge-hugging
  button** is recommended); scroll indicator only on scrolling screens.
- **Non-scrolling** screens (media players, pickers, steppers, dialogs,
  confirmations, fitness trackers) split into top, middle, and bottom; the
  middle stretches, top and bottom get inner margins against the curve.
  Prefer icon buttons over wide pill buttons; show key data graphically
  (progress indicators, large numbers). Paginate instead of scrolling when
  focus matters.
- **Scrolling** screens: `TransformingLazyColumn`; components fill the width;
  elevate unambiguous primary actions to the top of long pages; use labels to
  orient long mixed lists.
- Use icons **and** labels for actions when possible.

### Adaptive sizes

- Design for the **smallest round screen first** (204 to 216dp; check dense
  layouts at 192dp with large fonts).
- **Margins in percentages** on the outer edges; fixed dp between elements.
- **Breakpoint at 225dp:** below it, 192 to 224dp; above, 225 to 240dp+.
  After the breakpoint, add value: more content, more buttons, larger
  components, bolder graphs, the title slot on tiles. "Be expressive and bold."
- A larger screen must never show less than a smaller one. Don't just scale up;
  don't stretch components to fill space; don't enlarge fonts unless they're
  graphic.
- Quality tiers: ready for all screens (no broken layouts), responsive and
  optimized (fill the available space), adaptive and differentiated (new
  layouts past 225dp).

## Surfaces

### Tiles

- Immediate, predictable, relevant. Pick **one primary use case**; a few options
  plus "more" beats many options.
- At least one container, every container tappable and functional; no
  decorative containers; fewer containers when they lead to the same place.
- The system shows the app icon; don't draw it. Hide the title on small screens
  when two rows need 48dp targets; bring it back at 225dp.
- Show glanceable graphs, not detailed numbers.
- Design empty, sign-in, error, and no-data states with a clear call to action.
- Customize within the template so the tile carousel stays consistent.

### Media

- Media controls use a **5-button layout**: main play/pause 64dp (80dp at
  225dp+) with a progress ring; side controls; 2 bottom buttons (3 at 225dp+);
  overflow for more.
- Volume through the rotating side button or bezel; show the indicator only
  while rotating. Show the output device.
- Prioritize downloaded media (streaming drains the battery). Tiles show
  selectable media, not play/pause (tile updates lag up to 20 seconds).

### Always on and haptics

- Always-on layouts limit illuminated pixels and drop frequently updating
  progress indicators.
- Haptics: stronger for key moments (payment confirmation), subtler for
  precision (scrolling). Sync haptics with motion and sound.

### Surface priorities, watch faces, and notifications

From developer.android.com/design/ui/wear (M2.5 surfaces guides, still
current as platform behavior):

- **Split content by priority across surfaces.** Google's weather example: the
  complication answers P1 ("weather right now"), the tile adds P2 ("today"),
  the app adds P3 (hourly breakdown, preferences). Notifications carry only
  P1 alerts.
- **Tiles:** one task per tile (a fitness app ships a goals tile and a workout
  tile); show how fresh the data is ("45 min ago") when it's cached; update at
  most about once a minute for ongoing activities.
- **Watch faces:** time first (checked about 150 times a day), complications
  for glanceable data, customization, black as the primary color, stay within
  the bezel. Always-on mode lights **15% or less** of the pixels.
- **Notifications:** only when worth buzzing the wrist (valuable, glanceable,
  timely); standard, big text, big picture, and messaging templates.

## Behaviors (developer.android.com/design/ui/wear)

- **Navigation:** swipe right to close replaces back buttons. Keep everything
  else vertical; no horizontal carousels. Pannable views (maps) limit the
  dismiss swipe to a left-edge threshold.
- **Physical buttons:** map a multifunction button only to obvious binary
  single-press actions (start/stop, play/pause) in apps used without looking.
  Every mapped action also exists on screen; never map a destructive or
  multi-step action (deleting, stopping navigation, replying).
- **Launch:** black window background with the 48dp circular app icon
  centered (matching the launcher icon). Build the screen gradually: static
  text, buttons, and placeholders first; avoid indeterminate spinners; give
  visual feedback before the work completes.
- **Ongoing activities** (timers, workouts, media): the Recents entry states
  type and status (track name, workout duration, ETA); the tile shows a
  glanceable summary, not detail and actions.
- **Clipping:** test with other languages, larger text, and **Bold text**.
  Calls to action use text that fits the smallest screen; use compact chips
  instead of cards for dense layouts; pad lists so first and last items scroll
  fully into view.
- **Offline:** an offline indicator at the top when features are unavailable
  (gray them out or hide them), at the bottom of a list when no more content
  can load.
- **Sign-in:** Credential Manager with passkeys first, plus passwords and Sign
  in with Google; at least two distinct methods ("sign in on phone" alone fails
  without the phone). Sign-in-only apps show it immediately; others delay it
  until needed and explain the benefit in context. Never name "Credential
  Manager" in UI.
- **Dialogs:** alerts are full screen and interrupt, so use them sparingly;
  left-align alert text longer than three lines. Confirmations only
  acknowledge a finished action; they never ask for a decision.

## Gestures (Wear OS 7)

- **Double pinch** triggers the screen's primary action (answer a call, play or
  pause, snooze an alarm); **wrist turn** dismisses (back by default). Pixel
  Watch 3 and newer.
- Use for one-and-done interactions; never hide a gesture-only action (a
  visible button must do the same thing); no dead ends; scroll by gesture only
  when content is glanceable and reached hands-free.
- Floating hints use a tertiary container with onTertiary content; button hints
  match the content color. Gesture success plays a short, crisp haptic.

## Compose API reference (verified September 2026)

Verified against androidx-main (`bf95ca5`) and the released
`androidx.wear.compose:compose-material3:1.6.2` sources (1.7.0-rc01 adds the
`OneHandedGesture*` APIs). Everything below is stable and non-experimental.
Source root: `wear/compose/compose-material3/src/main/java/androidx/wear/compose/material3/`.

- **Theme:** `MaterialTheme(colorScheme, typography, shapes, motionScheme, content)`.
  **The default motion scheme is `MotionScheme.standard()`**; pass
  `MotionScheme.expressive()` to get bounce.
- **Springs** (damping / stiffness): expressive spatial fast 0.7 / 800, default
  0.75 / 350, slow 0.8 / 200; standard spatial 1.0 with 1400 / 500 / 260;
  effects no bounce with 1400 / 500 / 260. These differ from the phone values.
- **Dynamic color:** `dynamicColorScheme(context): ColorScheme?` returns null
  when unavailable, so fall back to your own scheme.
- **Shapes:** 4, 8, 18, 26, 36dp (differs from phone). No `MaterialShapes` on
  Wear.
- **Typography:** 21 styles: `arcLarge/Medium/Small` (`CurvedTextStyle`),
  `display*`, `title*`, `label*`, `bodyLarge` to `bodyExtraSmall`,
  `numeralExtraLarge` to `numeralExtraSmall`. `Typography(defaultFontFamily)`.
  Curved text: `curvedText(text, style = MaterialTheme.typography.arcMedium)`
  inside a `CurvedScope`.
- **Edge-hugging button:** `EdgeButton(onClick, buttonSize = EdgeButtonSize.Small, ...)`
  with sizes ExtraSmall 46, Small 56, Medium 70, Large 96dp; place it through
  `ScreenScaffold(edgeButton = ...)`.
- **Button group:** `ButtonGroup(...)`; in its scope, give each button its own
  `MutableInteractionSource`, apply `Modifier.animateWidth(interactionSource)`,
  and pass the same source to the button. Pressed buttons widen and squeeze
  their neighbors.
- **Shape morphing buttons:** `shapes = IconButtonDefaults.animatedShapes()`
  (also `TextButtonDefaults.animatedShapes()`, and toggle `animatedShapes()` /
  `variantAnimatedShapes()`).
- **Morphing lists:** `TransformingLazyColumn` with `rememberTransformationSpec()`;
  per item `Modifier.transformedHeight(this, spec)` and
  `transformation = SurfaceTransformation(spec)`. Items shrink as they near the
  top and bottom edges. A custom spec can override
  `applyContainerTransformation` (for example rotating items at the bottom);
  treat that as a hero-moment option, not a default.

## Open questions

1. Should the design skill cover Wear in its own reference file, or should
   `wear-compose-m3` point to this research?
