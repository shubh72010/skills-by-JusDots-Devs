---
name: centered-hero-carousel
description: 'Build a horizontally scrollable "hero carousel" where a big center card continuously morphs its width into different-size rectangles as it scrolls (the Material 3 / music-app "Quick Picks" effect). Explains why it is a pager with clipping + translation (not resizing), then gives three clearly-labeled fidelity tiers - exact Material replication, a faithful custom keyline implementation (no experimental API), and a scale-based approximation. Includes the real keyline interpolation math and cross-framework porting notes. Use when implementing hero carousels, morphing/expanding cards, centered featured-card rows, "big card shrinks as it scrolls", or cover-flow-style pager UIs.'
version: 1.1.0
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
  - keyline
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

## IMPLEMENTATION INVARIANT

> **Do not implement the morph by changing the measured width of the pager page.**
>
> The page's layout size stays constant. The visual effect is produced from the scroll
> position by:
>
> 1. determining the page center,
> 2. resolving it against keylines (the keyline before and the keyline after),
> 3. interpolating both the keyline's **size** and its **offset**,
> 4. clipping the page to the interpolated width,
> 5. translating the page to the interpolated offset,
> 6. applying z-order (focused page on top).
>
> Never use `Modifier.width()` driven by scroll, `animateDpAsState`, `animateContentSize`,
> or per-frame re-measurement. Those produce re-layout jank and are not this effect.

## How the effect actually works

The cards do **not** change size in layout, and this is **not** a `LazyRow`. It is a **pager**
of full-size pages where each page gets a **clip rectangle** (its visible width) plus a
**translation** that pins its clipped edges against its neighbors. Every scroll frame, the clip
width and translation are recomputed from the scroll offset — a smooth, fraction-of-a-pixel
interpolation, never a discrete step.

The mechanism:

1. **Pages are measured end-to-end at one fixed "hero" width.** The focused card is that width.
2. **Keylines** are fixed positions along the scroll axis that define the width a card should
   have when its center is there. For a centered hero the scheme is
   `[small | LARGE | small]`: the large keyline = hero width; the small keylines ≈ large/3
   (clamped, e.g. 40–56dp); tiny anchor keylines at the very edges keep cards from collapsing.
3. **Per-frame mask + translate + z-order** (see the math below).

## The math — keyline interpolation (framework-agnostic)

A **keyline** is a `(offset, size)` pair: the width a card should have when its center is at
`offset`. For a centered hero, relative to the focus position, with `H` = hero width, `W` =
small width, `S` = item spacing:

```
keylines = [ (-(H/2 + S + W/2), W),        // left small
             ( 0,                H),        // LARGE
             (+(H/2 + S + W/2), W) ]       // right small
```

For each visible page, `distanceFromFocus` is how many page-strides it sits from the focused
page (a continuous float during scroll). Then:

```
relCenter = distanceFromFocus * (H + S)            // page center vs. viewport center

before     = keyline with the largest offset <= relCenter
after      = keyline with the smallest offset >= relCenter
progress   = (relCenter - before.offset) / (after.offset - before.offset)
size       = lerp(before.size, after.size, progress)
keylineOff = lerp(before.offset, after.offset, progress)

clip width   = size                              // mask rect, centered on the page
translate    = keylineOff - relCenter            // pin the clipped edge to its neighbour
zIndex       = 1 / (1 + distanceFromFocus)
```

`size` and `translate` are **pure functions of the scroll position** — that is what makes the
morph continuous and cheap. Worked trace at rest: focused page `relCenter = 0` → size `H`,
translate `0`; neighbor at `relCenter = H + S` → size `W`; halfway between the large and small
keyline → size halfway between `H` and `W`. Because adjacent pages interpolate against the
*same shared* small keyline, their clipped edges stay pinned together with no gap.

---

# Choosing a fidelity tier

| Tier | Use when | Effect fidelity | Dependency |
|---|---|---|---|
| **1. Material replication** | Android/Compose, need the exact Material behavior (anchors, edge shifting, built-in snap) | Exact | `androidx.compose.material3` carousel |
| **2. Custom keyline** | Need the real morph but no experimental API, or custom keylines | Very close (same keyline + clip/translate mechanism) | `HorizontalPager` only |
| **3. Approximation (scale)** | Visual similarity is enough and simplicity matters | Similar look, proportional scaling, not width-only | `HorizontalPager` only |

---

## Tier 1 — Material replication (exact)

This is the reference implementation. It also handles snapping, RTL, and the end-of-list edge
cases (the first/last card grows large at the *edge* of the screen) for you.

**Dependency**: `androidx.compose.material3:material3` with the carousel APIs (present since
`1.3.0`; confirmed on `1.5.x`).

**Opt-in required**: `@OptIn(ExperimentalMaterial3Api::class)`.

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun HeroCarousel(
    itemCount: Int,
    heroWidth: Dp,
    carouselHeight: Dp,
    modifier: Modifier = Modifier,
    content: @Composable (index: Int) -> Unit,
) {
    HorizontalCenteredHeroCarousel(
        state = rememberCarouselState { itemCount },
        maxItemWidth = heroWidth,              // the LARGE keyline size
        itemSpacing = 10.dp,                   // gap between pages
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

**Typical card content** (full-bleed artwork + scrim + title — the Quick Picks look). `maskClip`
/ `maskBorder` come from `CarouselItemScope` and keep the corners correct while the mask morphs:

```kotlin
HorizontalCenteredHeroCarousel(...) { index ->
    val item = items[index]
    Box(
        Modifier
            .fillMaxSize()
            .maskClip(MaterialTheme.shapes.extraLarge)                  // rounded corners on the mask
            .maskBorder(BorderStroke(1.dp, outlineVariant), extraLarge) // optional outline
            .clickable { /* play */ },
    ) {
        AsyncImage(model = item.artwork, contentScale = ContentScale.Crop, modifier = Modifier.fillMaxSize())
        Box(                                                            // bottom scrim
            Modifier.fillMaxSize().background(
                Brush.verticalGradient(
                    0f to Color.Transparent,
                    0.48f to Color.Black.copy(alpha = 0.08f),
                    1f to Color.Black.copy(alpha = 0.84f),
                ),
            ),
        )
        Text(
            item.title,
            color = Color.White,
            fontWeight = FontWeight.Black,
            modifier = Modifier.align(Alignment.BottomStart).padding(16.dp),
        )
    }
}
```

**Tuning**:

| Param | Effect |
|---|---|
| `maxItemWidth` | Large keyline size. `Dp.Unspecified` = one large item filling the viewport minus two small items. For a hero slightly narrower than the screen, use `viewportWidth - 48.dp` (clamped). |
| `itemSpacing` | Gap between pages (e.g. `10.dp`). |
| `minSmallItemWidth` / `maxSmallItemWidth` | Clamp for the small side cards (defaults `40.dp` / `56.dp`). |
| `contentPadding` | Inset before the first / after the last page. |
| `flingBehavior` | Default `singleAdvanceFlingBehavior` = one card per swipe with `spring(MediumLow)` snap. |

---

## Tier 2 — Custom keyline implementation (no experimental API)

A faithful port of the same mechanism: pages stay at hero width, a `graphicsLayer` clips each
page to the interpolated keyline width and translates it to stay pinned. Reads the pager's
per-frame offset inside `graphicsLayer`, so it morphs without recomposition.

```kotlin
@Composable
fun KeylineHeroCarousel(
    itemCount: Int,
    heroWidth: Dp,
    carouselHeight: Dp,
    modifier: Modifier = Modifier,
    smallWidth: Dp = 56.dp,
    itemSpacing: Dp = 10.dp,
    content: @Composable (index: Int) -> Unit,
) {
    val density = LocalDensity.current
    val heroPx = with(density) { heroWidth.toPx() }
    val smallPx = with(density) { smallWidth.toPx() }
    val spacingPx = with(density) { itemSpacing.toPx() }
    val pagerState = rememberPagerState { itemCount }
    val mask = remember { MutableMaskRect() }
    val maskShape = remember(mask) { MaskRectShape(mask) }

    BoxWithConstraints(modifier = modifier) {
        val viewportPx = with(density) { maxWidth.toPx() }
        // The hero must fit inside the viewport, or the centering math is meaningless.
        val safeHeroPx = heroPx.coerceAtMost(viewportPx)
        val peekPx = ((viewportPx - safeHeroPx) / 2f).coerceAtLeast(0f)

        HorizontalPager(
            state = pagerState,
            pageSize = remember(safeHeroPx) {
                object : PageSize {
                    override fun Density.calculateMainAxisPageSize(
                        availableSpace: Int,
                        pageSpacing: Int,
                    ): Int = safeHeroPx.roundToInt()
                }
            },
            pageSpacing = itemSpacing,
            contentPadding = PaddingValues(horizontal = peekPx.dp),
            beyondViewportPageCount = 1,
            modifier = Modifier.fillMaxWidth().height(carouselHeight),
        ) { index ->
            val stride = safeHeroPx + spacingPx
            val smallOffset = safeHeroPx / 2f + spacingPx + smallPx / 2f
            // Keylines relative to the focus: [small | LARGE | small]
            val keylines = listOf(
                Keyline(-smallOffset, smallPx),
                Keyline(0f, safeHeroPx),
                Keyline(smallOffset, smallPx),
            )

            Box(
                modifier =
                    Modifier
                        .fillMaxSize()
                        .zIndex(1f / (1f + abs(pagerState.currentPage - index)))
                        .graphicsLayer {
                            // Per-frame distance from the focused page, in strides.
                            val distance =
                                (pagerState.currentPage - index) +
                                    pagerState.currentPageOffsetFraction
                            val relCenter = distance * stride

                            // Interpolate between the keylines bracketing this page's center.
                            val before = keylines.lastOrNull { it.offset <= relCenter }
                            val after = keylines.firstOrNull { it.offset >= relCenter }
                            val lo = before ?: after!!
                            val hi = after ?: before!!
                            val progress =
                                if (hi.offset > lo.offset) {
                                    ((relCenter - lo.offset) / (hi.offset - lo.offset))
                                        .coerceIn(0f, 1f)
                                } else {
                                    0f
                                }
                            val size = lerp(lo.size, hi.size, progress)
                            val keylineOffset = lerp(lo.offset, hi.offset, progress)

                            // Clip to the interpolated width (mask rect is page-local).
                            mask.set(
                                safeHeroPx / 2f - size / 2f,
                                0f,
                                safeHeroPx / 2f + size / 2f,
                                this.size.height,
                            )
                            this.shape = maskShape
                            clip = true

                            // Translate so the clipped center sits on the interpolated keyline.
                            translationX = keylineOffset - relCenter
                        },
            ) {
                content(index)
            }
        }
    }
}

private data class Keyline(val offset: Float, val size: Float)

private class MutableMaskRect {
    var left = 0f
    var top = 0f
    var right = 0f
    var bottom = 0f
    fun set(l: Float, t: Float, r: Float, b: Float) {
        left = l; top = t; right = r; bottom = b
    }
}

private class MaskRectShape(private val mask: MutableMaskRect) : Shape {
    override fun createOutline(
        size: Size,
        layoutDirection: LayoutDirection,
        density: Density,
    ): Outline = Outline.Rectangle(Rect(mask.left, mask.top, mask.right, mask.bottom))
}
```

Important details:

- The `graphicsLayer` block runs every draw frame; reading `pagerState.currentPageOffsetFraction`
  there gives the live offset **without recomposition** — that is what makes the morph buttery.
- The mask is a **rectangle** (width-only morph, height constant — the real effect). Rounded
  corners are NOT produced by `clip = true`; they come from the card content's own shape, so make
  each page's content a `Card`/`.clip(RoundedCornerShape(...))` of its own.
- The `clip = true` here only clips to the computed mask shape; the width morph is the mask,
  the height stays full.
- If your compiler flags `PageSize`, add `@OptIn(ExperimentalFoundationApi::class)` to the
  composable.
- Edge behavior: unlike Tier 1, the first/last card does not grow large at the screen edge — it
  simply stays small. Acceptable for short lists or when users rarely reach the ends.
- Snap: Pager snaps by default. To limit a fling to one card:
  `flingBehavior = PagerDefaults.flingBehavior(state = pagerState, pagerSnapDistance = PagerSnapDistance.atMost(1))`.

---

## Tier 3 — Approximation (proportional scaling)

> **Warning: this is NOT the same effect.** It scales the whole card proportionally (both
> dimensions), so rectangles keep their proportions instead of morphing width-only. Use it only
> when visual similarity is enough and the clip-based approaches aren't worth it.

Rounded corners come from the `Card`'s own shape (the `Card` clips itself) — `graphicsLayer
{ clip = true }` would only clip to the rectangular layer bounds, it does **not** create the
morphing mask.

```kotlin
@Composable
fun ScaledHeroCarousel(
    itemCount: Int,
    heroWidth: Dp,
    carouselHeight: Dp,
    modifier: Modifier = Modifier,
    itemSpacing: Dp = 10.dp,
    smallScale: Float = 0.72f,     // scale of the side cards
    edgeAlpha: Float = 0.65f,      // fade at the far edges
    content: @Composable (index: Int) -> Unit,
) {
    val density = LocalDensity.current
    val heroPx = with(density) { heroWidth.toPx() }
    val pagerState = rememberPagerState { itemCount }

    BoxWithConstraints(modifier = modifier) {
        val viewportPx = with(density) { maxWidth.toPx() }
        val safeHeroPx = heroPx.coerceAtMost(viewportPx)          // hero must fit the viewport
        val peekPx = ((viewportPx - safeHeroPx) / 2f).coerceAtLeast(0f)

        HorizontalPager(
            state = pagerState,
            pageSize = remember(safeHeroPx) {
                object : PageSize {
                    override fun Density.calculateMainAxisPageSize(
                        availableSpace: Int,
                        pageSpacing: Int,
                    ): Int = safeHeroPx.roundToInt()
                }
            },
            pageSpacing = itemSpacing,
            contentPadding = PaddingValues(horizontal = peekPx.dp),
            beyondViewportPageCount = 2,
            modifier = Modifier.fillMaxWidth().height(carouselHeight),
        ) { index ->
            Box(
                modifier =
                    Modifier
                        .fillMaxSize()
                        .graphicsLayer {
                            val distance =
                                (pagerState.currentPage - index) +
                                    pagerState.currentPageOffsetFraction
                            val progress = (1f - abs(distance)).coerceIn(0f, 1f)  // 1.0 = focused
                            scaleX = lerp(smallScale, 1f, progress)
                            scaleY = lerp(smallScale, 1f, progress)
                            translationX = distance * 24f                        // extra "pop"
                            alpha = lerp(edgeAlpha, 1f, progress)
                        },
            ) {
                Card(
                    shape = RoundedCornerShape(28.dp),   // rounding lives on the Card, not the layer
                    modifier = Modifier.fillMaxSize(),
                ) {
                    content(index)
                }
            }
        }
    }
}
```

---

## Porting to other frameworks

Same three ingredients, different vocabulary — and the keyline math above is the port.

- **SwiftUI**: `ScrollView(.horizontal)` + `onScrollGeometryChange` to read the offset, then
  per-view `scaleEffect` / `offset(x:)` / `zIndex` — or `TabView(PageStyle)` for snap. For the
  width-only morph use `clipShape` with a rectangle sized by `size` from the keyline math.
- **Flutter**: `PageView.builder` + `AnimatedBuilder` reading the fractional page offset, each
  page wrapped in `Transform.scale` / `Transform.translate`; `viewportFraction` controls the peeks.
- **Web/JS**: a scroll container reading `scrollLeft`; `transform: scale()` + `translateX()` +
  `z-index`, or `clip-path: inset()` for the width-only variant. `scroll-snap-type: x mandatory`
  for snapping.

Whichever stack you use, the invariant holds: **pages are full-size; you only ever change a clip
and a translate (plus z-order) as a pure function of scroll offset.**

---

## Customization & gotchas

- **Gap / small-size clamping**: keep the small keyline at roughly large/3 but clamped (40–56dp);
  otherwise side cards on a wide hero look comically tiny or too big to read as "peek".
- **Height must be fixed**: all pages share one row height (e.g. 332–380dp for phone/tablet); the
  morph changes only width. Give the carousel an explicit `height(...)`.
- **Round corners on the masked shape** (Tier 1: `maskClip`/`maskBorder`; Tier 2: a `Card`/clip
  inside the page), never on the unscaled page — otherwise the full-size page's corners slide
  around while it morphs.
- **Hero must fit the viewport** (Tier 2/3): clamp `heroWidth <= maxWidth` and derive the peeks
  from `BoxWithConstraints`, or the centering math is nonsense.
- **Edge behavior**: Tier 1 keeps the first/last card growing large at the screen edge via internal
  keyline "steps". Tiers 2–3 just show edge cards small.
- **Prefetch**: set `beyondViewportPageCount` (Tier 1 handles this internally) so neighbors are
  measured before they scroll into the morph region.
- **Accessibility**: keep each card a real tappable element with a label; the clip is purely
  visual and must not remove focus targets.

## Anti-patterns (do NOT do these)

- ❌ `Modifier.width(...)` animated from scroll position — re-layout churn, jank.
- ❌ `animateDpAsState` / `animateContentSize` driven by the current page — discrete, not the
  per-frame morph, and it fights the scroll.
- ❌ Re-measuring pages at different sizes per index in the pager — breaks the invariant.
- ❌ Building it on a `LazyRow` with per-item width snapshots — you lose the pinned-edge
  continuity and the buttery per-frame morph.