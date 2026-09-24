# Theming

Theme setup for Material 3 Expressive in Compose: versions, color schemes, typography, shapes, motion, and spacing. API names were checked against androidx sources (material3 1.5.0-alpha28) in September 2026.

## Versions

- `androidx.compose.material3:material3`: Expressive requires **1.5.0-alpha** or later. Stable 1.4.0 only has `ShortNavigationBar`, `WideNavigationRail`, and `ModalWideNavigationRail` from this skill.
- Toolchain: 1.5.0-alpha29 (and Wear Compose 1.7) require **compileSdk 37 and AGP 9.1+**; AGP 9.4 in turn needs Gradle 9.6. The `android create` template ships AGP 9.0.1, so raise these first or the build fails on dependency metadata checks.
- Check the newest version in [Maven metadata](https://dl.google.com/dl/android/maven2/androidx/compose/material3/material3/maven-metadata.xml) before adding it; alpha APIs still change (for example `Slider`'s `onValueChange` became required on main after alpha28, so pass it by name).
- Still `@ExperimentalMaterial3ExpressiveApi`: `MaterialShapes`, `RoundedPolygon.toShape()` / `toPath()`, `Morph.toPath()`, `LoadingIndicator`, `ContainedLoadingIndicator`, the pull-to-refresh loading indicator. Still `@ExperimentalMaterial3Api`: `AppBarWithSearch`, `carouselParallaxScrollEffect`, sheets, tooltips.
- Shape morphing uses `androidx.graphics:graphics-shapes`, which material3 already exposes.

## Theme

```kotlin
@Composable fun MaterialExpressiveTheme(
    colorScheme: ColorScheme? = null,
    motionScheme: MotionScheme? = null,
    shapes: Shapes? = null,
    typography: Typography? = null,
    content: @Composable () -> Unit,
)
```

- At the top level, nulls become `expressiveLightColorScheme()`, `MotionScheme.expressive()`, `Shapes()`, and `Typography()`. Nested, nulls inherit from the outer theme.
- `expressiveLightColorScheme()` is `lightColorScheme()` with more colorful `on*Container` roles. There is no dark counterpart; use `darkColorScheme()` or a generated scheme.
- Plain `MaterialTheme(...)` still works but defaults to `MotionScheme.standard()`.

## Color schemes

Pick one source per app, plus at most one content-based scheme per screen.

### Dynamic (wallpaper)

`dynamicLightColorScheme(context)` / `dynamicDarkColorScheme(context)` on API 31+. Google recommends it. Always provide a fallback scheme for older devices.

### Brand (seed color)

Compose has no seed or variant API. Two options:

1. **Static export:** generate light and dark schemes in Material Theme Builder from the brand color (or several: primary, secondary, tertiary, neutral) and paste the `lightColorScheme(...)` / `darkColorScheme(...)` values into the theme. Turn on "match color" (color fidelity) when the brand color must appear as-is.
2. **Runtime generation** with the third-party [MaterialKolor](https://github.com/jordond/MaterialKolor) library (`com.materialkolor:material-kolor`; check its latest version):

```kotlin
val colorScheme = rememberDynamicColorScheme(
    seedColor = BrandColor,
    isDark = dark,
    specVersion = ColorSpec.SpecVersion.SPEC_2025,
    style = PaletteStyle.Expressive, // or Vibrant, TonalSpot (Material's default), Fidelity, Content
)
```

Palette styles, from calm to loud: Monochrome, Neutral, TonalSpot (the stock Material look), Fidelity and Content (stay close to the seed), Vibrant (maximum chroma), Expressive (shifts hues for playful contrast), Rainbow, FruitSalad. For an app that should look distinct, start with Expressive or Vibrant.

A common pattern: brand scheme by default, dynamic color as a user setting.

### Content-based

Color a contained area from an image on screen (a media player from its album art, cards from their photos). Keep the source image visible where its color is used, limit a screen to two scheme sources, and never recolor semantic colors.

With MaterialKolor, extract a seed from the image and theme just that subtree:

```kotlin
val seed = remember(art) { art.themeColor(fallback = BrandColor) } // com.materialkolor.ktx, on an ImageBitmap
val scheme = rememberDynamicColorScheme(seedColor = seed, isDark = isSystemInDarkTheme(), style = PaletteStyle.Content)
MaterialExpressiveTheme(colorScheme = scheme) { NowPlaying(...) }
```

Decode a small thumbnail for extraction, not the full image. If the newest MaterialKolor fails to resolve against your Kotlin or Compose versions, pin the latest version that does.

### Roles

| Role | Use |
| --- | --- |
| `primary` / `onPrimary` | The most important action and active states |
| `primaryContainer` / `onPrimaryContainer` | Standout fills (FAB, selected hero element) |
| `secondary`, `secondaryContainer` | Supporting controls, selection, tonal buttons, filter chips |
| `tertiary`, `tertiaryContainer` | Contrasting accents: status, badges, progress, delight; vibrant menus and toolbars |
| `error`, `errorContainer` | Errors (static in every scheme) |
| `surface`, `surfaceContainerLowest` to `surfaceContainerHighest` | Body background, then nested containers by emphasis; nav areas use `surfaceContainer` |
| `onSurface`, `onSurfaceVariant` | Default text; lower-emphasis text and icons |
| `outline`, `outlineVariant` | Text field borders; dividers and decorative lines |
| `inverseSurface`, `inverseOnSurface`, `inversePrimary` | Snackbars and reversed elements |
| `primaryFixed`, `primaryFixedDim` (and secondary, tertiary) | Same tone in light and dark themes |

Semantic extras (a Success green): define as extra theme colors derived from a seed, with their own `on` and container pairs, instead of reusing `tertiary`.

## Typography

- 15 baseline styles plus 15 emphasized ones (`displayLargeEmphasized` ... `labelSmallEmphasized`, 1.5.0 only). Emphasized raises weight: Display, Headline, Body, and Title Large go Regular to Medium; Title Medium/Small and Labels go Medium to Bold.
- `Typography(fontFamily = ...)` sets one family everywhere. To put a brand face only on large styles, copy the defaults:

```kotlin
private val Brand = FontFamily(Font(R.font.brand_display_medium, FontWeight.Medium), Font(R.font.brand_display_bold, FontWeight.Bold))
private val base = Typography()

val AppTypography = base.copy(
    displayLarge = base.displayLarge.copy(fontFamily = Brand),
    displayMedium = base.displayMedium.copy(fontFamily = Brand),
    displaySmall = base.displaySmall.copy(fontFamily = Brand),
    headlineLarge = base.headlineLarge.copy(fontFamily = Brand),
    headlineMedium = base.headlineMedium.copy(fontFamily = Brand),
    headlineSmall = base.headlineSmall.copy(fontFamily = Brand),
    displayLargeEmphasized = base.displayLargeEmphasized.copy(fontFamily = Brand),
    headlineLargeEmphasized = base.headlineLargeEmphasized.copy(fontFamily = Brand),
    // repeat for the other Display and Headline emphasized styles you use
)
```

- Change letter spacing and line height to fit a new face, not sizes: sizes drive component layout.

### Google Sans Flex

Open source on [Google Fonts](https://fonts.google.com/specimen/Google+Sans+Flex) (since November 2025) and the typeface most shipped Expressive apps use. Axes: `wght` 1 to 1000, `wdth` 25 to 151, `opsz` 6 to 144, `slnt` -10 to 0, `GRAD` 0 to 100, `ROND` 0 to 100.

Bundle the variable TTF in `res/font/` (downloadable Google Fonts don't carry variation settings). Variation settings apply on API 26+. Because optical size should match each style's size, build the font per text style:

```kotlin
/** A Google Sans Flex voice: fixed axes, optical size matched to this style's font size. */
fun TextStyle.flex(weight: Int = 400, width: Float = 100f, round: Float = 0f, slant: Float = 0f) = copy(
    fontFamily = FontFamily(
        Font(
            resId = R.font.google_sans_flex,
            weight = FontWeight(weight),
            variationSettings = FontVariation.Settings(
                FontVariation.weight(weight),
                FontVariation.width(width),
                FontVariation.slant(slant),
                FontVariation.opticalSizing(fontSize),
                FontVariation.Setting("ROND", round),
            ),
        ),
    ),
    fontWeight = FontWeight(weight),
)

private val base = Typography()

val AppTypography = base.copy(
    displayLarge = base.displayLarge.flex(weight = 850, round = 100f),   // friendly hero
    displayMedium = base.displayMedium.flex(weight = 850, round = 100f),
    headlineLarge = base.headlineLarge.flex(weight = 500, width = 85f),  // premium display
    headlineMedium = base.headlineMedium.flex(weight = 500, width = 85f),
    titleLarge = base.titleLarge.flex(weight = 500),
    bodyLarge = base.bodyLarge.flex(),
    bodyMedium = base.bodyMedium.flex(),
    labelLarge = base.labelLarge.flex(weight = 500),
    // ...every style you use, including the *Emphasized ones with a heavier weight
)

// A voice used outside the type scale, such as tall numerals with state:
@Composable
fun numeralStyle(selected: Boolean) =
    MaterialTheme.typography.displayLarge.flex(weight = if (selected) 800 else 300, width = 35f)
        .copy(fontFeatureSettings = "tnum")
```

- Pick two or three voices from the table in SKILL.md section 5 and apply them consistently; don't invent new axis values per screen.
- State through axes: selected items heavy (700 to 900), unselected light (200 to 300); keep sizes equal so nothing reflows. `FontVariation.grade(...)` changes emphasis without changing width.
- Each distinct axis combination is a separate font instance; for animated axes (text that widens as a slider moves), step through a small set of values and keep it to a hero moment.
- Other voices that pair well: Google Sans Code or Roboto Mono for metadata and timecodes, Roboto Serif for long reading.
- Tabular numbers: `style.copy(fontFeatureSettings = "tnum")`.

## Shapes

`Shapes(extraSmall, small, medium, large, extraLarge, largeIncreased, extraLargeIncreased, extraExtraLarge)`, read as `MaterialTheme.shapes.*`:

| Token | Default | Typical use |
| --- | --- | --- |
| extraSmall | 4dp | Chips, snackbars |
| small | 8dp | Text fields, menus |
| medium | 12dp | Cards |
| large | 16dp | FABs, sheets on large screens |
| largeIncreased | 20dp | Expressive containers |
| extraLarge | 28dp | Dialogs, bottom sheets |
| extraLargeIncreased | 32dp | Expressive hero containers |
| extraExtraLarge | 48dp | Large hero surfaces |

Change a token to change every component mapped to it; pass `shape` to change one component.

### Shape library

`MaterialShapes` (experimental) has 35 `RoundedPolygon`s: Circle, Square, Slanted, Arch, Fan, Arrow, SemiCircle, Oval, Pill, Triangle, Diamond, ClamShell, Pentagon, Gem, Sunny, VerySunny, Cookie4Sided, Cookie6Sided, Cookie7Sided, Cookie9Sided, Cookie12Sided, Ghostish, Clover4Leaf, Clover8Leaf, Burst, SoftBurst, Boom, SoftBoom, Flower, Puffy, PuffyDiamond, PixelCircle, PixelTriangle, Bun, Heart.

Clip with `Modifier.clip(MaterialShapes.Sunny.toShape())` or use as a `Surface` shape.

### Shape morphing

Prefer components that morph on their own (buttons, icon buttons, toggles, button groups, list items, loading indicators). For a custom morph between two library shapes:

```kotlin
@OptIn(ExperimentalMaterial3ExpressiveApi::class)
private class MorphShape(private val morph: Morph, private val progress: Float) : Shape {
    override fun createOutline(size: Size, layoutDirection: LayoutDirection, density: Density): Outline {
        val path = morph.toPath(progress = progress) // unit-square coordinates
        path.transform(Matrix().apply { scale(size.width, size.height) })
        return Outline.Generic(path)
    }
}

@OptIn(ExperimentalMaterial3ExpressiveApi::class)
@Composable
fun MorphingBadge(done: Boolean, modifier: Modifier = Modifier) {
    val morph = remember { Morph(MaterialShapes.Circle, MaterialShapes.Cookie9Sided) }
    val progress by animateFloatAsState(if (done) 1f else 0f, MaterialTheme.motionScheme.defaultSpatialSpec())
    Box(modifier.size(64.dp).clip(MorphShape(morph, progress)).background(MaterialTheme.colorScheme.tertiaryContainer))
}
```

## Motion

`MotionScheme` has six specs: `defaultSpatialSpec`, `fastSpatialSpec`, `slowSpatialSpec`, `defaultEffectsSpec`, `fastEffectsSpec`, `slowEffectsSpec`. `MotionScheme.expressive()` and `MotionScheme.standard()` are the presets (values in SKILL.md section 7).

- Override one subtree by nesting `MaterialTheme(motionScheme = MotionScheme.standard()) { ... }` (`LocalMotionScheme` was removed).
- Reduced motion, Google's pattern:

```kotlin
object ReducedMotionScheme : MotionScheme {
    override fun <T> defaultSpatialSpec(): FiniteAnimationSpec<T> = snap()
    override fun <T> fastSpatialSpec(): FiniteAnimationSpec<T> = snap()
    override fun <T> slowSpatialSpec(): FiniteAnimationSpec<T> = snap()
    override fun <T> defaultEffectsSpec(): FiniteAnimationSpec<T> = snap()
    override fun <T> fastEffectsSpec(): FiniteAnimationSpec<T> = snap()
    override fun <T> slowEffectsSpec(): FiniteAnimationSpec<T> = snap()
}
```

- A custom brand scheme overrides the same six functions with `spring(dampingRatio, stiffness)`; keep effects specs at `Spring.DampingRatioNoBouncy`.

## Spacing

Compose has no spacing tokens. Define the 8dp scale once in the theme package and use it everywhere:

```kotlin
object Spacing {
    val xs = 4.dp
    val s = 8.dp
    val m = 12.dp
    val l = 16.dp
    val xl = 24.dp
    val xxl = 32.dp
    val xxxl = 48.dp
}
```

Screen margins: 16dp on compact, 24dp on medium and larger. Put padding and gaps on parent layouts (`Arrangement.spacedBy`) instead of margins on children.
