---
name: centered-hero-carousel
description: Build a horizontally scrollable "hero carousel" where a big center card continuously morphs its width into different-size rectangles as it scrolls (the Material 3 / music-app "Quick Picks" effect). Explains why it is a pager with clipping + translation (not resizing), gives a copy-paste Material3 implementation, a no-experimental-API fallback built on HorizontalPager, and the framework-agnostic math to port it to SwiftUI, Flutter, or the web. Use when implementing hero carousels, morphing/expanding cards, centered featured-card rows, "big card shrinks as it scrolls", or cover-flow-style pager UIs.
version: 1.0.0
author: JusDots Devs
domain: UI / interaction design
tags: carousel, pager, material3, jetpack-compose, animation, hero-card, android, swiftui, flutter
related_skills:
  - segmented-connect-list
  - adaptive
metadata:
  keywords:
  - hero carousel
  - morphing cards
  - centered carousel
  - pager animation
  - HorizontalCenteredHeroCarousel
  - cover flow
  - quick picks
---

# Centered Hero Carousel

A horizontal carousel where the focused card is a big rectangle and the cards beside it are
small rectangles that **continuously morph** as you scroll. At rest, the center card is large,
the neighbors peek out small on both sides, and a swipe advances one card with a spring snap.

This is the effect used by the "Quick Picks" section on music-app home screens.

## The core principle (understand this first)

The cards do **not** change size in layout, and this is **not** a `LazyRow`. The effect is a
**pager** of full-size pages where each page gets a **clip rectangle** (its visible width) plus a
**translation** that pins its clipped edges against its neighbors. Every scroll frame, the clip
width and translation are recomputed from the scroll offset — so the morph is a smooth,
fraction-of-a-pixel interpolation, never a discrete step.

Three ingredients:

1. **Pages laid out end-to-end at one fixed "hero" width** — every page is measured at the full
   hero size regardless of what you see.
2. **Keylines** — fixed positions on the scroll axis that define the width a card should have
   when its center is there. For a centered hero the scheme is `[anchor(small) | small | LARGE |
   small | anchor(small)]`. The large keyline = hero width; small keylines ≈ large/3 (clamped,
   e.g. 40–56dp); anchors are tiny peeks so cards never fully collapse.
3. **Per-frame mask + translate + z-order** — for each visible page, interpolate between the
   keylines before/after its center to get `interpolatedKeyline`, then:
   - **clip** the page to a rect of width `interpolatedKeyline.size`, centered on the page,
   - **translate** it by `interpolatedKeyline.offset - pageCenter` so clipped edges stay pinned
     to its neighbors (no gaps),
   - **draw the focused card on top** (`z = 1/(1+distanceFromFocus)`).

### The math (framework-agnostic)

```
pageSize   = heroWidth + spacing
pageCenter = index * pageSize + heroWidth/2 - scrollOffset

focus       = clamp(1 - abs(pageCenter - viewportCenter) / (heroWidth + smallWidth + spacing), 0, 1)
visibleWidth = lerp(smallWidth, heroWidth, focus)      // <- the morphing width
translate    = (pageCenter - viewportCenter) * 0.5     // pin edges to neighbours
zIndex       = 1 / (1 + distanceFromFocusPage)
```

Anything from raw Canvas to a scroll listener can implement this. The rest of this skill gives
battle-tested Compose implementations and porting notes.

---

## Option A — Material3 carousel (recommended, perfect replication)

This is the reference implementation. It handles snapping, RTL, anchors, and the end-of-list edge
cases (the first/last card becomes large at the edge of the screen) for you.

**Dependency**: `androidx.compose.material3:material3` with the carousel APIs (present since
`1.3.0`; confirmed on `1.5.x`).

**Opt-in required**:

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun QuickPicksCarousel(
    itemCount: Int,
    heroWidth: Dp,
    carouselHeight: Dp,
    modifier: Modifier = Modifier,
    content: @Composable (index: Int) -> Unit,
) {
    HorizontalCenteredHeroCarousel(
        state = rememberCarouselState { itemCount },
        maxItemWidth = heroWidth,             // the LARGE keyline size
        itemSpacing = 10.dp,                  // gap between pages
        contentPadding = PaddingValues(horizontal = 16.dp),
        modifier =
            modifier
                .fillMaxWidth()
                .height(carouselHeight),
    ) { index ->
        content(index)
    }
}
```

**Typical card content** (full-bleed artwork + scrim + title, the Quick Picks look):

```kotlin
HorizontalCenteredHeroCarousel(...) { index ->
    val song = items[index]
    Box(
        Modifier
            .fillMaxSize()
            .maskClip(MaterialTheme.shapes.extraLarge)                 // rounded corners
            .maskBorder(BorderStroke(1.dp, outlineVariant), extraLarge) // optional outline
            .clickable { /* play */ },
    ) {
        AsyncImage(model = song.artwork, contentScale = ContentScale.Crop, modifier = Modifier.fillMaxSize())
        Box(                                                          // bottom scrim
            Modifier.fillMaxSize().background(
                Brush.verticalGradient(
                    0f to Color.Transparent,
                    0.48f to Color.Black.copy(alpha = 0.08f),
                    1f to Color.Black.copy(alpha = 0.84f),
                ),
            ),
        )
        Text(
            song.title,
            color = Color.White,
            fontWeight = FontWeight.Black,
            modifier = Modifier.align(Alignment.BottomStart).padding(16.dp),
        )
    }
}
```

`maskClip`/`maskBorder` come from `androidx.compose.material3.carousel.CarouselItemScope` — they
round the mask rectangle correctly while it morphs.

**Tuning**:

| Param | Effect |
|---|---|
| `maxItemWidth` | Size of the large keyline. `Dp.Unspecified` = one large item filling the viewport minus two small items. Set it to `viewportWidth - 48.dp` (clamped) for a hero that's slightly narrower than the screen. |
| `itemSpacing` | Gap between pages (e.g. `10.dp`). |
| `minSmallItemWidth` / `maxSmallItemWidth` | Clamp for the small side cards (defaults `40.dp`/`56.dp`). |
| `contentPadding` | Inset before the first / after the last page. |
| `flingBehavior` | Default = `singleAdvanceFlingBehavior` (one card per swipe, `spring(MediumLow)` snap). |

---

## Option B — No experimental API (fallback, `HorizontalPager` + scale)

Use this when you can't depend on the carousel API or need something simpler. It produces a very
close look — big focused card, smaller side cards that grow/shrink while scrolling — via a
`graphicsLayer` scale. Trade-off vs Option A: it scales both dimensions (aspect-preserving) rather
than clipping width-only, so the side cards are smaller but keep their proportions. Set
`beyondViewportPageCount` so neighbors are composed in time to animate in.

```kotlin
@Composable
fun CenteredHeroCarousel(
    itemCount: Int,
    heroWidth: Dp,
    carouselHeight: Dp,
    modifier: Modifier = Modifier,
    itemSpacing: Dp = 10.dp,
    smallScale: Float = 0.72f,        // scale of the side cards
    edgeAlpha: Float = 0.65f,         // fade at the far edges
    content: @Composable (index: Int) -> Unit,
) {
    val density = LocalDensity.current
    val pagerState = rememberPagerState { itemCount }
    val heroWidthPx = with(density) { heroWidth.toPx() }

    BoxWithConstraints(modifier = modifier) {
        val peek = (maxWidth - heroWidth) / 2      // equal peeks on both sides
        HorizontalPager(
            state = pagerState,
            pageSize = object : PageSize {         // pages are exactly heroWidth
                override fun Density.calculateMainAxisPageSize(availableSpace: Int, pageSpacing: Int) =
                    heroWidthPx.roundToInt()
            },
            pageSpacing = itemSpacing,
            contentPadding = PaddingValues(horizontal = peek.coerceAtLeast(0.dp)),
            beyondViewportPageCount = 2,
            modifier = Modifier.fillMaxWidth().height(carouselHeight),
        ) { index ->
            val pageOffset =
                (pagerState.currentPage - index) + pagerState.currentPageOffsetFraction
            val progress = (1f - abs(pageOffset)).coerceIn(0f, 1f)   // 1.0 = focused
            Box(
                modifier =
                    Modifier
                        .fillMaxSize()
                        .graphicsLayer {
                            scaleX = lerp(smallScale, 1f, progress)
                            scaleY = lerp(smallScale, 1f, progress)
                            translationX = pageOffset * 24f           // extra "pop" feel
                            alpha = lerp(edgeAlpha, 1f, progress)
                            clip = true                               // keep corners sharp
                        },
            ) {
                content(index)
            }
        }
    }
}
```

Notes:

- `pagerState.currentPage - index + pagerState.currentPageOffsetFraction` gives a continuous
  -1..1-ish distance even mid-gesture, so the scale animates with every scroll frame.
- For a width-only morph (height stays constant, rectangles change proportions — closer to
  Option A), clip instead of scale: keep pages at `fillMaxSize` and apply a `Modifier.layout`
  that sets the visible mask width to `lerp(smallWidthPx, heroWidthPx, progress)` and translates
  by `(1f - progress) * (heroWidthPx - smallWidthPx) / 2f` to close the gaps.
- Snapping: Pager snaps by default. To limit a fling to one card, pass
  `flingBehavior = PagerDefaults.flingBehavior(state = pagerState, pagerSnapDistance = PagerSnapDistance.atMost(1))`.

---

## Porting to other frameworks

Same three ingredients, different vocabulary:

- **SwiftUI**: `ScrollView(.horizontal)` + `onScrollGeometryChange` to read `scrollOffset`, then
  per-view `scaleEffect`, `offset(x:)`, `zIndex`, and `.clipShape` — or `TabView(PageStyle)` for
  snap. Compute `progress` and `visibleWidth` with the math above.
- **Flutter**: `PageView.builder` with `itemBuilder` each wrapped in a `Transform.scale` +
  `Transform.translate` driven by `pageController.position` / an `AnimatedBuilder` reading the
  fractional scroll offset. Use `viewportFraction` to control how much of the neighbors peek.
- **Web/JS**: a scroll container with `scrollLeft` → `progress = 1 - |cardCenter - viewportCenter|/w`,
  then `transform: scale()` + `translateX()` + `z-index`, or `clip-path: inset()` for the
  width-only variant. `scroll-snap-type: x mandatory` for snapping.

Whichever stack you use, the invariant is: **pages are full-size; you only ever change a clip and
a translate (plus z-order) as a pure function of scroll offset.**

---

## Customization & gotchas

- **Gap / small-size clamping**: keep the small keyline at roughly large/3 but clamp it
  (40–56dp) — otherwise side cards on a wide hero look comically tiny or too big to read as "peek".
- **Rounding corners**: apply corner rounding to the *masked* shape (Option A: `maskClip` /
  `maskBorder`; Option B: `clip = true` in `graphicsLayer`), not to the unscaled page, or you'll
  see the full-size page's corners slide around while it morphs.
- **Height must be fixed**: all pages share one row height (e.g. 332–380dp for a phone/tablet);
  the morph changes only width. Give the carousel an explicit `height(...)`.
- **Don't force resize in layout** — it causes re-layout churn and visible jank. The clip/translate
  approach is purely visual, so it runs at 60/120fps without recomposing.
- **Edge behavior**: Option A keeps the first/last card becoming large at the screen edge via
  internal keyline "steps". The Option B fallback simply shows edge cards small — acceptable when
  the list has a handful of items or users rarely reach the ends.
- **Accessibility / semantics**: keep each card a real clickable/tappable element with a label;
  the clip is purely visual and should not remove focus targets.
- **Prefetch**: set `beyondViewportPageCount`/prefetch distance so off-screen neighbors are
  measured before they scroll into the morph region.