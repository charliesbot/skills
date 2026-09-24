# Wear OS

Material 3 Expressive on watches, from the Wear OS design guide and androidx sources (Wear compose-material3 1.6.2 stable, checked September 2026). The phone guidance on emphasis, hero moments, and restraint still applies; this file covers what changes on a round, glanceable, battery-bound screen. For scaffolding (`AppScaffold`, `ScreenScaffold`), navigation, and migration, use the `wear-compose-m3` skill.

## Levels of expression

Google grades Wear apps and asks for **excellent or transformative**, and says "don't let your app or tile be unremarkable":

| | Excellent (required) | Transformative (recommended) |
| --- | --- | --- |
| Components | Multiple expressive components | Plus customization |
| Color | Dynamic palette themes | Plus unexpected color combinations |
| Typography | Roboto Flex | Roboto Flex with morphing (animated weight or width) |
| Shape | Some shape library and containment | Expressive containment |
| Motion | Tokens plus springs and shape morphing | Same |
| Hero moments | Product-specific expressive moments | Plus dramatic hierarchy |
| Adaptive | Percentage margins, added value past 225dp | Plus large-screen-specific designs |

## Principles

- **One or two critical tasks**, finished in seconds. No dense data, grids, or spreadsheets.
- **Glanceable:** test while moving; tiles get about 7 seconds of attention.
- **Shallow and vertical:** at most two levels deep; scroll in one direction only.
- **Embrace round:** edge-hugging buttons and bezel-hugging elements (progress rings, scroll indicators, curved time text) use the curve instead of fighting it.

## Theme

```kotlin
@Composable
fun WearAppTheme(content: @Composable () -> Unit) {
    val context = LocalContext.current
    MaterialTheme(
        colorScheme = dynamicColorScheme(context) ?: BrandWearColorScheme,
        motionScheme = MotionScheme.expressive(), // Wear defaults to standard
        content = content,
    )
}
```

(`androidx.wear.compose.material3.MaterialTheme`, `MotionScheme`, and `dynamicColorScheme`.)

- `dynamicColorScheme(context)` returns null when the watch face provides no theme; always fall back.
- Wear springs (damping / stiffness): expressive spatial fast 0.7 / 800, default 0.75 / 350, slow 0.8 / 200; standard spatial 1.0 with 1400 / 500 / 260; effects never bounce.
- Wear corner scale: 4, 8, 18, 26, 36dp. There is no `MaterialShapes` on Wear.

## Color

- **Build from black.** App backgrounds are black; tiles never use full-bleed images or color.
- Dynamic themes come from two watch-face seed colors (primary and tertiary) and meet WCAG AAA. Pair only `X` with `onX`.
- Each accent has base, **dim**, and container roles. Primary: the main action and edge button. PrimaryDim: distinct but not demanding. Secondary: supporting actions in dense UI. Tertiary: standout feedback (tap responses, a goal reached). ErrorDim: emergencies and stop buttons.
- Recommended pairings: Primary with PrimaryDim (main plus complementary); PrimaryDim with Tertiary (highlight plus feedback); Primary with SecondaryContainer (main stands out, rest recedes); Tertiary plus Primary with SecondaryContainer when there's no single main action.
- Tiles: primary on the primary action, secondary or tertiary elsewhere; never all filled primary buttons.

## Typography

- Roboto Flex everywhere. Weight and width are the useful axes: narrow width fits long names and numbers; no wide type in headers; no very light weights at small sizes.
- Roles: `display*` (hero information and metrics), `title*` (wayfinding), `label*` (inside components), `bodyLarge` to `bodyExtraSmall`, `numeralExtraLarge` to `numeralExtraSmall` (a few digits that need no localization, tabular by default, can take expressive width), `arcLarge/Medium/Small` (curved text at the top or bottom edge).
- Curved text: `curvedText(text, style = MaterialTheme.typography.arcMedium)` inside a `CurvedScope`.
- Line height about 1.1x. Nothing 20sp and up scales with the user font setting; check the largest and smallest settings.

## Layout

- Top: time text (not on dialogs, confirmations, or pickers) and an optional title. Middle: content. Bottom: actions, ideally an **edge button**. Scroll indicator only on scrolling screens.
- Margins in **percentages** at the outer edges, fixed dp between elements.
- Design for the smallest round screen first (204 to 216dp; check 192dp with large fonts). **Breakpoint at 225dp:** add value past it (more content, bigger controls, the tile title). A larger screen must never show less. Don't stretch or scale up components; don't enlarge fonts unless they're graphic.
- Non-scrolling screens (media, pickers, timers, dialogs): top, middle, bottom sections, the middle stretching; prefer icon buttons over wide pills; show data graphically.
- Elevate unambiguous primary actions to the top of long scrolling pages; label sections in long mixed lists.

## Expressive components

| Pattern | API |
| --- | --- |
| Edge-hugging button | `ScreenScaffold(scrollState, edgeButton = { EdgeButton(onClick, buttonSize = EdgeButtonSize.Medium) { ... } })`; sizes ExtraSmall 46, Small 56, Medium 70, Large 96dp |
| Morph on press | `FilledIconButton(onClick, shapes = IconButtonDefaults.animatedShapes()) { ... }` (also `TextButtonDefaults.animatedShapes()`, toggle `animatedShapes()`) |
| Button group | `ButtonGroup { ... }`: one `MutableInteractionSource` per button, `Modifier.animateWidth(interactionSource)`, and the same source passed to the button; the pressed button widens and squeezes its neighbors |
| Morphing list | `TransformingLazyColumn` with `val spec = rememberTransformationSpec()`; per item `Modifier.transformedHeight(this, spec)` and `transformation = SurfaceTransformation(spec)` |

Custom `TransformationSpec` effects (rotating or morphing items at the edges) are hero-moment material, not a default.

## Surfaces

- **Tiles:** one primary use case; a few options plus "more"; every container tappable with a clear outcome; no decorative containers; the system draws the app icon; hide the title on small screens when two rows need 48dp targets; glanceable graphs, not detailed numbers; design empty, sign-in, and error states.
- **Media:** five-button controls with play/pause at 64dp (80dp past 225dp) inside a progress ring; volume via the rotating side button; prioritize downloaded media; tiles show selectable media, not play/pause.
- **Always-on:** few lit pixels; drop frequently updating progress.
- **Haptics:** strong for key moments (payment confirmed), subtle for precision (scrolling), synced with motion.
- **Gestures (Wear OS 7):** double pinch triggers the screen's one primary action, wrist turn dismisses; every gesture action must also have a visible button.
