# Components

Which component to use, how Google says to use it, and the Expressive Compose API. Names and parameters were checked against androidx sources (material3 1.5.0-alpha28) in September 2026. Components marked (experimental) need `@OptIn(ExperimentalMaterial3ExpressiveApi::class)`.

## Selection guide

| Need | Component | Rules that matter |
| --- | --- | --- |
| Page title and 1 or 2 actions | App bar: search, small, medium flexible, large flexible | One action, two at most; boost the key one with a filled or tonal (optionally wide) icon button, never two filled; fills with a contrasting color on scroll, or stays transparent with filled icon buttons floating over content; flexible bars compress to small on scroll |
| Many actions for the current page | Toolbar: floating (contextual) or docked (global) | Standard colors keep focus on content, vibrant colors emphasize controls or signal a mode like editing; never with a navigation bar on screen; floating can pair with a FAB and collapse on scroll |
| The single most important action | FAB (FAB, medium, large) | One per screen; not every screen needs one; stays put on scroll; bottom half of the screen; color from primary, secondary, or tertiary sets |
| Labeled primary action on a long scroll | Extended FAB (small, medium, large) | One per screen; not inside a set of actions; not with a floating toolbar |
| 2 to 6 related actions behind the FAB | FAB menu | Only from a regular FAB; not with a toolbar or rail; color set matches the FAB |
| Discrete actions | Buttons: elevated, filled, tonal, outlined, text; XS to XL; round or square | Don't overuse; three at most in one arrangement; primary action gets more size, color, or shape |
| Related buttons that react together | Standard button group | Same size and shape by default; mixed sizes only for hero moments; different shape only for selection or meaning |
| Choose an option or switch views | Connected button group | Items must be toggleable; one color style for the whole group |
| Main action plus alternatives | Split button | Menu aligned to the trailing half, 4dp away |
| Common icon actions | Icon buttons: filled, tonal, outlined, standard; XS 32 to XL 136dp; narrow, uniform, wide | Filled sparingly; equally important buttons share a size; outlined icon when off, filled when toggled on |
| Contextual choices, filters, entered items | Chips: assist, filter, input, suggestion | Never for Save or Cancel; always in a set; can scroll horizontally |
| Content about one topic | Cards: elevated, filled, outlined | Don't force content into cards when spacing or headings would do; container transform to detail only for hero moments; no internal scroll on phones |
| Visual collection | Carousel: multi-browse, uncontained, hero, centered hero, full-screen | Snap scrolling except uncontained; at most 3 items with text on phones; offer "Show all" |
| Scannable items | Expressive list, standard or segmented | Align leading visuals and text; gaps for contained lists, dividers only for uncontained ones; one selection mode at a time |
| Temporary set of actions | Vertical menu | Group with gaps or dividers; vibrant (tertiary) colors sparingly |
| 3 to 5 top destinations (phone) | Flexible navigation bar | Labels always; fewer than 3 destinations means tabs; no swiping between destinations; reselecting scrolls to top |
| Destinations on larger windows | Navigation rail, collapsed (3 to 7 items) or expanded | Leading edge, outside panes; one navigation component per screen; expanded replaces the drawer |
| Related content on one level | Tabs, primary and secondary | Not for sequential content; avoid swipeable content inside |
| Supplementary content | Bottom sheet (phones), side sheet (medium and up) | Drag handle cycles heights; scrim tap closes |
| Blocking decision | Dialog | Rare; low-priority messages go to a snackbar |
| Brief feedback | Snackbar | One at a time, one action, above the FAB and navigation |
| Wait of 200ms to 5s | Loading indicator | Under 200ms show nothing; over 5s use a progress indicator; one indicator per group |
| On/off setting | Switch | Takes effect immediately; not for opposing options (use a connected button group) |
| Pick one or several | Radio buttons (5 or fewer) or checkboxes | Vertical lists; one radio always selected |
| Value in a range | Slider: standard, centered, range | Takes effect immediately; no vertical range sliders |
| Text entry | Text field, filled or outlined | Don't mix the two in one form |
| Search | Search bar, search app bar (global), or search icon button | Group results with gaps; full screen on phones, docked on tablets |

## Component details

### Lists

- Align leading visuals and primary text in the same position on every row; put visuals at the leading edge, never mid-row.
- People get circular or expressive-shape avatars (`MaterialShapes`); products and videos get square or rectangular images.
- An item with an image can take a content-based container color from that image, on its enabled state or on interaction.
- Supporting text: one to three lines. Trailing text for price, count, or date. Leading or trailing checkbox (multi-select), radio (single-select), or switch (settings).
- Primary action takes most of the row; secondary actions (bookmark, overflow) go trailing.
- Show selection with two cues, never color alone (a checkmark plus a fill).
- Swipe reveals mixed-style buttons with the primary action last; a full swipe triggers it; always offer another path (overflow).
- Settings rows with a switch: use the `onClick` overload of `SegmentedListItem` with a trailing `Switch` (clicking the row toggles it). The `checked` overload gives the whole row a checkbox role and a selected fill.
- Segmented items must stand off their background: on a `surface` page, pass `colors = ListItemDefaults.segmentedColors(containerColor = MaterialTheme.colorScheme.surfaceContainerHigh)`.
- Compact: edge-to-edge list opening a full detail page. Medium and up: list-detail side by side; a list can become cards or a carousel on large screens.

### Menus

- A temporary set of actions; persistent actions belong in a toolbar.
- Unavailable items show disabled, not removed.
- Gaps group items more expressively than dividers: at most one or two gaps, fixed size, never in a scrolling menu (use dividers there).
- Slots (image, swatch, progress) only for simple content; never a button or switch inside an item.
- On compact screens, long or complex menus can become a bottom sheet; on larger screens, menus stay in context and can use submenus.
- The trigger keeps its look and shows a pressed state while the menu is open.

### Search

- Entry point by importance: search app bar when search is the product's main job, a search bar below the title for one view's content, a search icon button when secondary.
- Contained style: container `surfaceContainerHigh` on a `surface` background (never on `surfaceContainer`); 24dp margins unfocused, 12dp focused.
- Leading: a navigation icon button or a non-functional search icon. Trailing: at most two icons (voice, profile avatar, overflow).
- Hint text names what is searchable: "Search your messages," not "Search."
- Results and suggestions are lists: add leading icons, category labels (Recent, Contacts), avatars, filter chips, and gaps between groups.
- Full-screen results on compact, docked results on medium and up; keep the query visible after searching.

## Expressive API map

| Component | Compose API | Notes |
| --- | --- | --- |
| Flexible app bars | `MediumFlexibleTopAppBar`, `LargeFlexibleTopAppBar` | `subtitle`, `titleHorizontalAlignment`; `TopAppBarDefaults.exitUntilCollapsedScrollBehavior()` |
| Search app bar | `AppBarWithSearch` | `@ExperimentalMaterial3Api` |
| Contained search | `SearchBar(state, inputField)`, `rememberContainedSearchBarState()`, `SearchBarDefaults.containedColors(state)`, `ExpandedFullScreenContainedSearchBar` | |
| Floating toolbar | `HorizontalFloatingToolbar`, `VerticalFloatingToolbar` | FAB overload; `FloatingToolbarDefaults.vibrantFloatingToolbarColors()`, `VibrantFloatingActionButton`, `exitAlwaysScrollBehavior(FloatingToolbarExitDirection.Bottom)`, `Modifier.floatingToolbarVerticalNestedScroll(...)`, `ScreenOffset` |
| Docked toolbar | `FlexibleBottomAppBar` | |
| FABs | `FloatingActionButton`, `MediumFloatingActionButton`, `LargeFloatingActionButton`, `SmallExtendedFloatingActionButton`, `MediumExtendedFloatingActionButton`, `LargeExtendedFloatingActionButton` | |
| FAB menu | `FloatingActionButtonMenu`, `FloatingActionButtonMenuItem`, `ToggleFloatingActionButton` | `Modifier.animateIcon({ checkedProgress })` in the toggle's scope |
| Buttons | `Button`, `ElevatedButton`, `FilledTonalButton`, `OutlinedButton`, `TextButton` with `shapes =` | Heights: `ButtonDefaults.ExtraSmallContainerHeight` 32, `MinHeight` 40, `MediumContainerHeight` 56, `LargeContainerHeight` 96, `ExtraLargeContainerHeight` 136; `shapesFor(h)`, `contentPaddingFor(h)`, `iconSizeFor(h)`, `textStyleFor(h)` |
| Toggle buttons | `ToggleButton`, `ElevatedToggleButton`, `FilledTonalToggleButton`, `OutlinedToggleButton` | `ToggleButtonSize`, `ToggleButtonDefaults.shapesFor(...)` |
| Standard button group | `ButtonGroup(overflowIndicator = { ButtonGroupDefaults.OverflowIndicator(it) })` | Scope: `clickableItem`, `toggleableItem`, `customItem`, `Modifier.animateWidth(interactionSource)` |
| Connected button group | `Row` of `ToggleButton`s | `ButtonGroupDefaults.ConnectedSpaceBetween`, `connectedLeadingButtonShapes()`, `connectedMiddleButtonShapes()`, `connectedTrailingButtonShapes()` |
| Split button | `SplitButtonLayout` | `SplitButtonDefaults.LeadingButton`, `TrailingButton(checked, onCheckedChange)`; tonal, outlined, elevated variants |
| Icon buttons | `IconButton(shapes = IconButtonDefaults.shapes())` and filled/tonal/outlined variants | Size with `Modifier.size(IconButtonDefaults.smallContainerSize(IconButtonDefaults.IconButtonWidthOption.Wide))` (the width option is nested in `IconButtonDefaults`); extraSmall to extraLarge; `Narrow`, `Uniform`, `Wide`. App bar actions use small; medium and up are for hero controls |
| Loading indicator (experimental) | `LoadingIndicator`, `ContainedLoadingIndicator` | Also pull-to-refresh |
| Wavy progress | `LinearWavyProgressIndicator`, `CircularWavyProgressIndicator` | Determinate and indeterminate; `amplitude`, `wavelength`, `waveSpeed`. Keep the default strokes: a thick custom stroke on a small circle turns the wave lumpy. Scale the indicator's size, not its stroke |
| Lists | `ListItem(onClick, ...) { headline }`, `SegmentedListItem(shapes = ListItemDefaults.segmentedShapes(i, n))` | `ListItem(headlineContent = ...)` is deprecated; segmented items spaced by `ListItemDefaults.SegmentedGap` |
| Vertical menus | `DropdownMenuPopup`, `DropdownMenuGroup(shapes = MenuDefaults.groupShape(i, n))`; items: `DropdownMenuItem(onClick, text, shape)` for actions, `SelectableDropdownMenuItem` for single choice (radio role), `CheckableDropdownMenuItem` for multi-choice | `MenuDefaults.itemShape(i, n)`; vibrant: `itemVibrantColors()`, `selectableItemVibrantColors()`, `groupVibrantContainerColor`; submenus by nesting `DropdownMenuPopup` with `MenuDefaults.rememberDropdownMenuPopupPositionProvider(...)` |
| Navigation bar | `ShortNavigationBar`, `ShortNavigationBarItem(iconPosition = NavigationItemIconPosition.Top)` | `Start` icon position for medium windows |
| Navigation rail | `WideNavigationRail(state = rememberWideNavigationRailState())`, `WideNavigationRailItem(railExpanded = ...)`, `ModalWideNavigationRail` | `NavigationSuiteScaffold` swaps bar and rail by window size |
| Slider | `Slider(state = s, onValueChange = { ... })`, `VerticalSlider` | No XS to XL presets; inset icons are drawn in a custom track |
| Carousel | `HorizontalMultiBrowseCarousel`, `HorizontalUncontainedCarousel`, `HorizontalCenteredHeroCarousel` | `rememberCarouselState` |

## Snippets

### Flexible app bar

```kotlin
val scrollBehavior = TopAppBarDefaults.exitUntilCollapsedScrollBehavior()
Scaffold(
    modifier = Modifier.nestedScroll(scrollBehavior.nestedScrollConnection),
    topBar = {
        LargeFlexibleTopAppBar(
            title = { Text("Habits") },
            subtitle = { Text("3 left today") },
            navigationIcon = { /* back icon button */ },
            actions = {
                FilledTonalIconButton(onClick = onAdd, shapes = IconButtonDefaults.shapes()) {
                    Icon(Icons.Rounded.Add, contentDescription = "Add habit")
                }
            },
            scrollBehavior = scrollBehavior,
        )
    },
) { padding -> /* content */ }
```

### Sized, morphing primary button

```kotlin
val height = ButtonDefaults.ExtraLargeContainerHeight
Button(
    onClick = onStart,
    shapes = ButtonDefaults.shapesFor(height),
    modifier = Modifier.fillMaxWidth().heightIn(height),
    contentPadding = ButtonDefaults.contentPaddingFor(height),
) {
    Text("Start session", style = ButtonDefaults.textStyleFor(height))
}
```

### Floating toolbar with a FAB

```kotlin
Box(Modifier.fillMaxSize()) {
    content()
    HorizontalFloatingToolbar(
        expanded = expanded,
        floatingActionButton = {
            FloatingToolbarDefaults.VibrantFloatingActionButton(onClick = onCompose) {
                Icon(Icons.Rounded.Edit, contentDescription = "Compose")
            }
        },
        colors = FloatingToolbarDefaults.vibrantFloatingToolbarColors(),
        modifier = Modifier.align(Alignment.BottomEnd).offset(x = -FloatingToolbarDefaults.ScreenOffset, y = -FloatingToolbarDefaults.ScreenOffset),
    ) {
        IconButton(onClick = onArchive) { Icon(Icons.Rounded.Archive, contentDescription = "Archive") }
        IconButton(onClick = onLabel) { Icon(Icons.Rounded.Label, contentDescription = "Label") }
    }
}
```

Collapse on scroll by attaching `Modifier.floatingToolbarVerticalNestedScroll(expanded, onExpand = { expanded = true }, onCollapse = { expanded = false })` to the scrolling container.

### FAB menu

```kotlin
var open by rememberSaveable { mutableStateOf(false) }
FloatingActionButtonMenu(
    expanded = open,
    button = {
        ToggleFloatingActionButton(checked = open, onCheckedChange = { open = it }) {
            val icon = if (checkedProgress > 0.5f) Icons.Rounded.Close else Icons.Rounded.Add
            Icon(icon, contentDescription = null, modifier = Modifier.animateIcon({ checkedProgress }))
        }
    },
) {
    FloatingActionButtonMenuItem(onClick = onPhoto, text = { Text("Photo") }, icon = { Icon(Icons.Rounded.PhotoCamera, null) })
    FloatingActionButtonMenuItem(onClick = onNote, text = { Text("Note") }, icon = { Icon(Icons.Rounded.Notes, null) })
}
```

### Connected button group

```kotlin
Row(horizontalArrangement = Arrangement.spacedBy(ButtonGroupDefaults.ConnectedSpaceBetween)) {
    options.forEachIndexed { index, label ->
        ToggleButton(
            checked = selected == index,
            onCheckedChange = { selected = index },
            shapes = when (index) {
                0 -> ButtonGroupDefaults.connectedLeadingButtonShapes()
                options.lastIndex -> ButtonGroupDefaults.connectedTrailingButtonShapes()
                else -> ButtonGroupDefaults.connectedMiddleButtonShapes()
            },
            modifier = Modifier.weight(1f).semantics { role = Role.RadioButton },
        ) { Text(label) }
    }
}
```

### Standard button group

```kotlin
ButtonGroup(overflowIndicator = { ButtonGroupDefaults.OverflowIndicator(it) }) {
    clickableItem(onClick = onPrevious, label = "Previous", icon = { Icon(Icons.Rounded.SkipPrevious, null) })
    toggleableItem(checked = playing, label = "Play", onCheckedChange = onPlayToggle, icon = { Icon(Icons.Rounded.PlayArrow, null) }, weight = 2f)
    clickableItem(onClick = onNext, label = "Next", icon = { Icon(Icons.Rounded.SkipNext, null) })
}
```

### Split button

```kotlin
SplitButtonLayout(
    leadingButton = { SplitButtonDefaults.LeadingButton(onClick = onSend) { Text("Send") } },
    trailingButton = {
        SplitButtonDefaults.TrailingButton(checked = menuOpen, onCheckedChange = { menuOpen = it }) {
            Icon(Icons.Rounded.KeyboardArrowDown, contentDescription = "More send options")
        }
    },
)
```

### Segmented list

```kotlin
Column(verticalArrangement = Arrangement.spacedBy(ListItemDefaults.SegmentedGap)) {
    settings.forEachIndexed { index, setting ->
        SegmentedListItem(
            onClick = { onOpen(setting) },
            shapes = ListItemDefaults.segmentedShapes(index, settings.size),
            leadingContent = { Icon(setting.icon, null) },
            supportingContent = { Text(setting.summary) },
        ) { Text(setting.title) }
    }
}
```

Separate groups with a larger gap (16 to 24dp) between columns of segmented items.

### Grouped vertical menu

```kotlin
DropdownMenuPopup(expanded = open, onDismissRequest = { open = false }) {
    val groups = listOf(listOf("Rename", "Duplicate"), listOf("Delete"))
    groups.forEachIndexed { groupIndex, items ->
        DropdownMenuGroup(shapes = MenuDefaults.groupShape(groupIndex, groups.size)) {
            items.forEachIndexed { i, item ->
                DropdownMenuItem(
                    onClick = { onAction(item) },
                    text = { Text(item) },
                    shape = MenuDefaults.itemShape(i, items.size).shape,
                )
            }
        }
        if (groupIndex < groups.lastIndex) Spacer(Modifier.height(MenuDefaults.GroupSpacing))
    }
}
```

### Loading and progress

```kotlin
@OptIn(ExperimentalMaterial3ExpressiveApi::class)
@Composable
fun Loading() = Box(Modifier.fillMaxSize(), contentAlignment = Alignment.Center) { ContainedLoadingIndicator() }

LinearWavyProgressIndicator(progress = { playback.fraction }, modifier = Modifier.fillMaxWidth())
```

### Navigation by window size

```kotlin
NavigationSuiteScaffold(
    navigationItems = {
        destinations.forEach { dest ->
            NavigationSuiteItem(
                selected = dest == current,
                onClick = { onNavigate(dest) },
                icon = { Icon(dest.icon, contentDescription = null) },
                label = { Text(dest.label) },
            )
        }
    },
) { /* current destination */ }
```

Use the `navigationItems` + `NavigationSuiteItem` overload (from `material3-adaptive-navigation-suite`, same 1.5.0 versions) rather than the older `navigationSuiteItems` scope overload. For explicit control, use `ShortNavigationBar` on compact and `WideNavigationRail` on medium and up.
