# Android UI Rendering Internals
## Measure → Layout → Draw, 60 FPS, and Rendering Optimization

Senior Android Developer – Deep Dive Notes

---

# 1. Overview of Android View Rendering

Android UI rendering follows a **three-step pipeline**:

```
Measure → Layout → Draw
```

This pipeline determines:

- How big views are
- Where they appear
- How they are rendered

Every UI component ultimately extends:

```
android.view.View
```

Example hierarchy:

```
View
├── TextView
├── ImageView
├── Button
└── ViewGroup
```

Containers such as:

- LinearLayout
- FrameLayout
- ConstraintLayout

extend **ViewGroup**.

---

# 2. Complete Rendering Pipeline

When a screen is rendered:

```
Activity.setContentView()
        │
        ▼
View hierarchy created
        │
        ▼
Measure phase
        │
        ▼
Layout phase
        │
        ▼
Draw phase
```

Internally this is triggered by:

```
ViewRootImpl
```

Pipeline inside ViewRootImpl:

```
performTraversals()
    │
    ├── performMeasure()
    ├── performLayout()
    └── performDraw()
```

---

# 3. Measure Phase

## Purpose

Determine how big each view should be.

Method called:

```
onMeasure()
```

Signature:

```java
onMeasure(int widthMeasureSpec, int heightMeasureSpec)
```

---

## MeasureSpec

MeasureSpec contains:

```
Size + Mode
```

| Mode | Meaning |
|----|----|
| EXACTLY | Parent defines exact size |
| AT_MOST | Child can be up to this size |
| UNSPECIFIED | No constraint |

Example:

```
Parent View
     │
     ▼
Child receives MeasureSpec
     │
     ▼
Child calculates width/height
```

Views must call:

```java
setMeasuredDimension(width, height);
```

Failure to call this can cause the view **not to render**.

---

# 4. Layout Phase

## Purpose

Determine **where views appear on screen**.

Method called:

```java
onLayout(boolean changed, int left, int top, int right, int bottom)
```

Example result:

```
left = 0
top = 0
right = 200
bottom = 100
```

These represent the **view boundaries**.

Parent views position children during this stage.

Example:

```
LinearLayout
     │
     ├── child1
     ├── child2
     └── child3
```

LinearLayout calculates positions sequentially.

---

# 5. Draw Phase

## Purpose

Actually **render pixels on screen**.

Method called:

```java
onDraw(Canvas canvas)
```

Canvas allows drawing operations.

Example:

```java
canvas.drawText()
canvas.drawRect()
canvas.drawCircle()
```

---

## Rendering Order

```
View background
      │
      ▼
onDraw()
      │
      ▼
Children drawn
      │
      ▼
Foreground decorations
```

---

# 6. Rendering Flow Summary

Complete pipeline:

```
Activity.setContentView()

View hierarchy created
        │
        ▼
measure()
        │
        ▼
layout()
        │
        ▼
draw()
```

Each view goes through:

```
onMeasure()
onLayout()
onDraw()
```

---

# 7. Hardware Acceleration

Modern Android uses **GPU rendering**.

Introduced in **Android 3.0 (Honeycomb)**.

Rendering pipeline:

```
App Code
   │
   ▼
View Hierarchy
   │
   ▼
Display List
   │
   ▼
RenderThread
   │
   ▼
GPU Rendering
```

Benefits:

- Faster drawing
- Smooth animations
- Reduced CPU usage

---

# 8. View Invalidation

When a view changes, Android must redraw it.

Method:

```java
invalidate()
```

Flow:

```
invalidate()
     │
     ▼
View marked dirty
     │
     ▼
Draw scheduled
```

---

# requestLayout()

Another method:

```java
requestLayout()
```

Difference:

| Method | Effect |
|---|---|
| invalidate() | redraw view |
| requestLayout() | re-run measure + layout + draw |

Flow:

```
requestLayout()
      │
      ▼
measure()
      │
      ▼
layout()
      │
      ▼
draw()
```

---

# 9. Choreographer and Frame Rendering

Android aims for **60 FPS rendering**.

Frame duration:

```
1000 ms / 60 ≈ 16.6 ms
```

Each frame must complete within **~16 ms**.

---

## Frame Rendering Pipeline

```
VSYNC Signal
      │
      ▼
Choreographer
      │
      ▼
Input Handling
      │
      ▼
Animations
      │
      ▼
Measure/Layout/Draw
      │
      ▼
RenderThread
      │
      ▼
GPU
      │
      ▼
Display
```

If frame exceeds **16 ms**:

```
Frame Drop → UI Jank
```

---

# 10. What is VSYNC?

VSYNC is a **hardware display signal**.

Typical displays refresh at:

```
60Hz
```

Meaning:

```
Screen refreshes every 16.6 ms
```

Android synchronizes rendering with this signal.

Purpose:

- Avoid tearing
- Maintain smooth animations

---

# 11. Overdraw Problem

Overdraw occurs when **pixels are drawn multiple times**.

Example:

```
Window background
     │
     ▼
Parent layout background
     │
     ▼
Child view background
```

Same pixel rendered **multiple times**.

Problems:

- GPU waste
- Slower rendering
- Battery drain

---

## Fix Overdraw

Best practices:

- Remove unnecessary backgrounds
- Flatten view hierarchy
- Use ConstraintLayout
- Avoid nested layouts

Tools:

```
Developer Options → GPU Overdraw
Layout Inspector
```

---

# 12. Custom View Rendering

Developers can create custom views.

Example:

```java
class CustomView extends View {

    Paint paint = new Paint();

    @Override
    protected void onDraw(Canvas canvas) {

        canvas.drawCircle(100,100,50,paint);
    }
}
```

Used for:

- charts
- animations
- custom UI components

---

# 13. Performance Optimization Tips

## Avoid Deep View Hierarchies

Bad example:

```
LinearLayout
 └── LinearLayout
      └── LinearLayout
           └── TextView
```

More nodes = more measure/layout passes.

Better:

```
ConstraintLayout
     └── TextView
```

---

## Avoid Heavy Work in onDraw()

Bad example:

```java
onDraw() {
    new Paint();
}
```

Object allocation inside `onDraw()` causes:

- GC pressure
- Frame drops

Best practice:

```
Create objects once
Reuse during draw
```

---

## Use RecyclerView Efficiently

RecyclerView improves performance through:

- ViewHolder pattern
- View recycling
- LayoutManager
- Prefetching

---

# 14. Common Causes of UI Jank

UI jank occurs when frame exceeds **16 ms**.

Common causes:

- Heavy work on main thread
- Excessive layout passes
- Overdraw
- Large bitmap rendering
- Frequent object allocations

---

# 15. Real Interview Questions

## Q1: Explain Android View Rendering Pipeline

Android renders UI using **Measure → Layout → Draw** phases.

- Measure determines size
- Layout determines position
- Draw renders pixels onto the canvas

---

## Q2: Difference between invalidate() and requestLayout()

| Method | Description |
|---|---|
| invalidate() | redraw view |
| requestLayout() | re-measure + re-layout |

---

## Q3: Why is 16 ms important?

Android targets **60 FPS**.

```
1000 ms / 60 ≈ 16.6 ms
```

Each frame must finish within this time.

---

## Q4: What causes multiple layout passes?

Examples:

- Nested layouts
- requestLayout called repeatedly
- LinearLayout weights
- Dynamic content changes

---

## Q5: What is Overdraw?

Overdraw occurs when a pixel is rendered multiple times.

Example:

```
Window background
Parent layout background
Child background
```

Detect using:

- GPU Overdraw tool
- Layout Inspector

---

# 16. Advanced Rendering Question

## What triggers rendering?

Rendering is triggered by **Choreographer** on each VSYNC signal.

Pipeline:

```
VSYNC
   │
   ▼
Choreographer
   │
   ▼
Input
   │
   ▼
Animations
   │
   ▼
Measure/Layout/Draw
   │
   ▼
RenderThread
   │
   ▼
GPU
```

---

# 17. Debug Question

## Why might a custom view not appear?

Possible causes:

- `setMeasuredDimension()` not called
- Wrong layout params
- `invalidate()` not triggered
- Drawing outside bounds
- Alpha or visibility issues

---

# 18. System Design Question (Ride Apps)

Example question:

**How would you render hundreds of drivers on a map smoothly?**

Expected answer:

- Marker clustering
- Throttling updates
- Diff updates
- Avoid full map redraw
- Batch UI updates

---

# 19. Trick Question

## Why might a view be measured twice?

Examples:

- wrap_content inside ScrollView
- LinearLayout weight usage
- Constraint resolution loops

---

# 20. Best 30-Second Interview Answer

Android renders UI using a **Measure → Layout → Draw pipeline**.

During **measure**, each view determines its size based on parent constraints.

During **layout**, the parent positions its children on screen.

During **draw**, views render their content onto a canvas.

This process is synchronized with the system's **Choreographer and VSYNC signal** to maintain smooth rendering at approximately **60 frames per second**.

---

# Key Takeaways

- Android UI uses **Measure → Layout → Draw**
- Rendering synchronized with **VSYNC**
- Each frame must finish within **16 ms**
- Overdraw and deep hierarchies slow rendering
- Optimize drawing and avoid heavy work on main thread