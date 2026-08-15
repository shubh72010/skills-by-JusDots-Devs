---
name: segmented-connect-list
description: Build a Jetpack Compose list where each row looks separate but connected, like a single rounded panel split into segments. Each row is its own Card, spaced 2dp apart, with corner radii that change per position — big (28dp) rounded corners on the group's outer edges and small (6dp) inner corners, so the group reads as one unified panel. Use when implementing segmented/grouped row lists, connected pill lists, split-list UI, "separate but connected" rows, or grouped cards with rounded corners.
version: 1.0.0
author: JusDots Devs
domain: Android UI development
tags: jetpack-compose, ui, segmented-list, material3, android
related_skills:
  - adaptive
  - styles
metadata:
  keywords:
  - Jetpack Compose
  - segmented list
  - connected rows
  - grouped cards
  - rounded corners
  - split list
  - panel list
---

# Segmented Connect List

Creates a list of rows that looks like a single rounded panel split into separate segments —
each song/item name looks separate but connected, with big rounded corners on the outer
edges of the group and nearly-square corners where rows meet.

## How the illusion works

The look is built from three things acting together:

1. **Each row is its own Card** with `surfaceContainerLow` (or another single uniform fill)
   and **0dp elevation** — so the whole group reads as one flat panel, not separate pills.
2. **Rows are spaced only 2dp apart** (`Arrangement.spacedBy(2.dp)`). Close enough that the
   gaps read as seams between segments, not separations.
3. **Corner radii change per position.** The group's outer corners are strongly rounded
   (`large = 28.dp`) while inner corners near the neighboring row are nearly square
   (`small = 6.dp`). This is the same shape pattern Material uses for segmented / split buttons.

## Shape logic (the core)

```kotlin
private val GroupLargeCorner = 28.dp // outer corners of the group
private val GroupSmallCorner = 6.dp  // inner corners (near the next row)

private fun segmentedShape(index: Int, count: Int): Shape =
    when {
        count <= 1 -> RoundedCornerShape(GroupLargeCorner)               // lone row: fully rounded
        index == 0 -> RoundedCornerShape(                                // first row
            topStart = GroupLargeCorner,
            topEnd = GroupLargeCorner,
            bottomEnd = GroupSmallCorner,
            bottomStart = GroupSmallCorner,
        )
        index == count - 1 -> RoundedCornerShape(                        // last row
            topStart = GroupSmallCorner,
            topEnd = GroupSmallCorner,
            bottomEnd = GroupLargeCorner,
            bottomStart = GroupLargeCorner,
        )
        else -> RoundedCornerShape(GroupSmallCorner)                     // middle rows
    }
```

## Generic implementation

Any row content can be used (song row, setting row, contact, etc.) via the `content` slot.

```kotlin
@Composable
fun <T> SegmentedConnectList(
    items: List<T>,
    key: (T) -> Any?,
    content: @Composable (item: T, index: Int) -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(
        verticalArrangement = Arrangement.spacedBy(2.dp), // <-- tiny seam gap
        modifier =
            modifier
                .fillMaxWidth()
                .padding(horizontal = 12.dp, vertical = 2.dp), // group inset
    ) {
        items.forEachIndexed { index, item ->
            Card(
                shape = segmentedShape(index = index, count = items.size),
                colors = CardDefaults.cardColors(
                    containerColor = MaterialTheme.colorScheme.surfaceContainerLow,
                ),
                elevation = CardDefaults.cardElevation(defaultElevation = 0.dp),
                modifier = Modifier.fillMaxWidth(),
            ) {
                content(item, index)
            }
        }
    }
}
```

## Usage example

```kotlin
SegmentedConnectList(
    items = songs.take(6),
    key = { it.id },
) { song, index ->
    SongRow(
        number = index + 1, // left-hand number badge reinforces the "list" feel
        song = song,
        modifier = Modifier.fillMaxWidth(),
    )
}
```

## Customization notes

- **Gap size** controls how "connected" it reads: `0.dp` = one seamless card, `2dp` = segmented
  seams, larger gaps start to look like separate cards again.
- **Colors**: use one uniform container color across all rows (e.g. `surfaceContainerLow`).
  If you want the whole group to be one color regardless of theme, pass the same color to
  `CardDefaults.cardColors`.
- **Corner sizes**: keep `large` about 4–5× `small` for a strong segmented effect
  (28dp / 6dp works well). Reduce both for compact groups.
- **Whole group as a single panel**: put the group inside a `Card` with `GroupLargeCorner`
  and the rows without their own outer padding — then the outer card supplies the big corners.
- **Interactions**: put `combinedClickable`/`clickable` on each row's Card, not on the container,
  so each segment is independently tappable.