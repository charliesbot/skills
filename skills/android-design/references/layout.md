# Layout

Adaptive layout for phones, foldables, tablets, and desktop windows, from Material's layout guidance (2026 layout scaffold update). For Compose adaptive APIs in depth (MediaQuery, Grid, FlexBox, Navigation 3 scenes), also load the `adaptive` and `navigation-3` skills.

## Breakpoints

Design for window size, not devices; orientation, folding, split screen, and free-form windows change it at runtime.

| Breakpoint | Width | Panes | Navigation | Margins |
| --- | --- | --- | --- | --- |
| Compact | under 600dp | 1 | Navigation bar (or modal expanded rail) | 16dp |
| Medium | 600 to 839dp | 1 recommended; 2 only for low-density content, 50/50 | Collapsed rail with 1 pane; nav bar with 2 panes | 24dp, 24dp spacer |
| Expanded | 840 to 1199dp | 2 recommended | Collapsed or expanded rail | 24dp, 24dp spacer |
| Large | 1200 to 1599dp | 2 recommended | Rail, collapsed or expanded | 24dp, 24dp spacer |
| Extra large | 1600dp+ | 2 (3 with a side sheet) | Expanded rail | 24dp, 24dp spacer |

Moving up a breakpoint, ask five questions:

1. **Reveal:** what hidden UI can appear (expanded rail, second pane, more list detail)? "Additional space doesn't just mean making the same thing bigger."
2. **Divide:** one pane or two?
3. **Resize:** cards, feeds, lists, keeping text at 40 to 60 characters per line.
4. **Reposition:** move actions from the bottom to the leading edge, add negative space, keep controls reachable (avoid the top 25% of a tablet held in landscape).
5. **Swap:** nav bar to rail, bottom sheet to menu, full-screen dialog to basic dialog, FAB to extended FAB. Only swap functionally equivalent components.

## Scaffold

- **Bars** frame the page: the app bar at the top, the navigation bar at the bottom, next to system safety regions (status and gesture bars). Content never goes in safety regions (handle insets; see the `edge-to-edge` skill).
- **Rails** surround panes. On compact screens the bottom rail region above the nav bar holds toolbars, chat inputs, and FABs. On large screens the leading rail holds navigation and the trailing rail holds page controls (a vertical floating toolbar).
- **Panes** hold all content: 1 to 3, at least one flexible. Fixed panes are 360dp (expanded) or 412dp (large and up). In split layouts, keep the spacer visually centered even with a rail.
- Panes display **co-planar** (side by side), **floating** (like a dialog), or **docked** (like a bottom sheet), and adapt by **show and hide**, **levitate** (become floating or docked), or **reflow** (stack vertically).
- Panes can blend into the background (implicit grouping) or use surface colors (explicit). In multi-pane layouts use surface roles to show which pane is primary, for example `surfaceBright` for the active pane beside a `surfaceDim` rail.

## Canonical layouts

- **Feed:** a grid of cards; one column on compact, more columns as width grows; lead items can span columns to create hierarchy.
- **List-detail:** one pane on compact (list or detail), two panes from expanded. Show the selection state only in two-pane mode and the back button only in single-pane mode; keep scroll position when switching; show an empty state in the detail pane when nothing is selected. Navigation 3: `ListDetailSceneStrategy` with entries tagged `ListDetailScene.ListPane` and `ListDetailScene.DetailPane` gives this plus predictive back.
- **Supporting pane:** primary content about two-thirds; the supporting pane sits below it on compact and medium (a bottom sheet works on compact) and beside it at 360dp on expanded.

## Navigation by size

- Compact: `ShortNavigationBar` with vertical items (icon above label).
- Medium: `ShortNavigationBar` with horizontal items (`NavigationItemIconPosition.Start`) for two-pane layouts, or a collapsed `WideNavigationRail` for one pane.
- Expanded and up: `WideNavigationRail`, collapsed or expanded; the expanded rail replaces the navigation drawer. `ModalWideNavigationRail` for compact screens that need more than five destinations.
- `NavigationSuiteScaffold` switches automatically.
- One navigation component per screen; never a rail and a bar together.

## Density and text

- Density is a user choice, not an automatic breakpoint change. Never increase density on focused tasks (menus) or alerts (dialogs, snackbars).
- Support 200% text scaling: text and line height grow, padding stays; stack side-by-side buttons and allow vertical scrolling when text grows.
