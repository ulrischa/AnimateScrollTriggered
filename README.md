# Animate Scroll Triggered

A tiny CSS-only scroll-trigger adapter for [Animate.css](https://animate.style/).

Animate.css stays unchanged. You keep using the normal Animate.css classes and add one extra class, `.animate-triggered`, to control when the animation is played while scrolling.

## What this does

Animate.css animations are normal time-based CSS animations. This adapter does not turn them into scroll-scrubbed animations. Instead, it starts the existing Animate.css animation when the element crosses a scroll trigger range.

That makes it useful for reveal effects such as cards, sections, teasers, tiles, and small scrollytelling steps.

## Files

```txt
animate-scroll-triggered.css  The CSS adapter
index.html                    Demo page
README.md                     Documentation
```

## Basic usage

Load Animate.css first. Load the adapter after it.

```html
<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css"
>
<link rel="stylesheet" href="animate-scroll-triggered.css">
```

Then add `.animate-triggered` to an existing Animate.css element.

```html
<article class="animate__animated animate__fadeInUp animate-triggered">
  Content
</article>
```

The Animate.css classes still define the effect:

```html
<article class="animate__animated animate__zoomIn animate-triggered">
  Content
</article>
```

## Presets

### Default

```html
<article class="animate__animated animate__fadeInUp animate-triggered">
  Content
</article>
```

The default trigger waits until the element has entered the viewport. The animation stays active until the element leaves the viewport.

```css
--animate-trigger-activation: entry 100% exit 0%;
--animate-trigger-active: entry 0% exit 100%;
```

### Early

```html
<article class="animate__animated animate__fadeInLeft animate-triggered animate-triggered--early">
  Content
</article>
```

Starts earlier, while the element is still entering the viewport.

```css
--animate-trigger-activation: entry 60% exit 0%;
```

### Late

```html
<article class="animate__animated animate__fadeInDown animate-triggered animate-triggered--late">
  Content
</article>
```

Starts later, when the element is clearly inside the viewport.

```css
--animate-trigger-activation: contain 25% contain 75%;
```

### Play forward only

```html
<article class="animate__animated animate__fadeInUp animate-triggered animate-triggered--once">
  Content
</article>
```

By default, the animation plays forward when the trigger activates and backward when the trigger deactivates. Add `.animate-triggered--once` when the animation should only play forward.

## Per-element control

You can override the trigger range on each element.

```html
<article
  class="animate__animated animate__zoomIn animate-triggered"
  style="--animate-trigger-activation: entry 80% exit 0%; --animate-duration: 1.8s;"
>
  Content
</article>
```

Animate.css custom properties still work normally, for example `--animate-duration`.

## Trigger a group of direct children

Add `.animate-triggered` to a container when the container should provide the trigger and its direct animated children should play together.

```html
<div class="grid animate-triggered">
  <div class="animate__animated animate__fadeInUp">1</div>
  <div class="animate__animated animate__fadeInUp">2</div>
  <div class="animate__animated animate__fadeInUp">3</div>
</div>
```

For staggered effects, set `animation-delay` or Animate.css timing variables on the children.

```css
.grid > :nth-child(2) {
  animation-delay: 0.14s;
}

.grid > :nth-child(3) {
  animation-delay: 0.28s;
}
```

## Why `trigger-scope` is used

The adapter uses the same internal trigger name, `--animate-trigger`, for every `.animate-triggered` element.

Without `trigger-scope`, repeated trigger names can conflict because triggers are globally visible. `trigger-scope` limits the visibility of the trigger to the element where it is declared.

## Browser compatibility

This adapter uses CSS Scroll-Triggered Animations:

- `timeline-trigger`
- `animation-trigger`
- `trigger-scope`

At the time this README was written, this is a very new CSS feature. Chrome describes scroll-triggered animations as arriving in Chrome 145 and explains that these are time-based animations triggered when crossing a scroll offset.

The adapter is wrapped in `@supports`, so browsers without support will ignore the trigger logic. In unsupported browsers, Animate.css may behave like normal page-load Animate.css unless you add your own fallback.

This repository intentionally does not include a JavaScript fallback. If you need production support across current stable browsers, use an `IntersectionObserver` fallback or keep this as a progressive enhancement.

## CSS adapter

```css
@layer animate-scroll-triggered {
  @supports (animation-trigger: --animate-trigger play-forwards) and
            (timeline-trigger: --animate-trigger view() entry 100% exit 0%) {
    :where(.animate-triggered) {
      --animate-trigger-activation: entry 100% exit 0%;
      --animate-trigger-active: entry 0% exit 100%;

      trigger-scope: --animate-trigger;

      timeline-trigger:
        --animate-trigger
        view()
        var(--animate-trigger-activation)
        /
        var(--animate-trigger-active);

      animation-trigger:
        --animate-trigger
        play-forwards
        play-backwards;
    }

    :where(.animate-triggered) > :where(.animate__animated) {
      animation-trigger:
        --animate-trigger
        play-forwards
        play-backwards;
    }

    :where(.animate-triggered--early) {
      --animate-trigger-activation: entry 60% exit 0%;
    }

    :where(.animate-triggered--late) {
      --animate-trigger-activation: contain 25% contain 75%;
    }

    :where(.animate-triggered--once) {
      animation-trigger:
        --animate-trigger
        play-forwards;
    }

    :where(.animate-triggered--once) > :where(.animate__animated) {
      animation-trigger:
        --animate-trigger
        play-forwards;
    }
  }
}
```

## License

No license has been added yet.
