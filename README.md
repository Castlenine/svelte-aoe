<div align="center">

# `@castlenine/svelte-aoe`

[![npm.badge]][npm] [![download.badge]][download]

A Svelte component to animate elements, without dependencies
</div>

`@castlenine/svelte-aoe` utilizes the [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to detect when an element enters the viewport.

When the element is detected as being in the viewport, `@castlenine/svelte-aoe` applies a class that triggers a CSS animation.

## 🚀 [Demo](https://castlenine-svelte-aoe.vercel.app/)

## Installation

Use your package manager to install:

```bash
npm i @castlenine/svelte-aoe --save-dev
```

## Setup

- Import the package

```svelte
import AnimateOnEnter from '@castlenine/svelte-aoe';
```

- Add the component to your layout/page/component.

```svelte
<AnimateOnEnter />
```

- Add a `data-aoe` attribute to the element that you want to animate and define an animation.

```svelte
<img data-aoe="fade-up" src="https://dummyimage.com/500x300"/>
```

## Example: SvelteKit Global Setup

File: `src/routes/+layout.svelte`

```svelte
<script>
  import AnimateOnEnter from '@castlenine/svelte-aoe';
</script>

<AnimateOnEnter />

<slot />
```

## Animations

### Normal speed

- `up`
- `right`
- `left`
- `down`
- `fade`
- `fade-up`
- `fade-right`
- `fade-left`
- `fade-down`

### Fast speed

- `up-fast`
- `right-fast`
- `left-fast`
- `down-fast`
- `fade-fast`
- `fade-up-fast`
- `fade-right-fast`
- `fade-left-fast`
- `fade-down-fast`

### Slow speed

- `up-slow`
- `right-slow`
- `left-slow`
- `down-slow`
- `fade-slow`
- `fade-up-slow`
- `fade-right-slow`
- `fade-left-slow`
- `fade-down-slow`

### Normal speed partial fade

Start wih `0.25` opacity

- `partial-fade`
- `partial-fade-up`
- `partial-fade-right`
- `partial-fade-left`
- `partial-fade-down`

### Fast speed partial fade

Start wih `0.25` opacity

- `partial-fade-fast`
- `partial-fade-up-fast`
- `partial-fade-right-fast`
- `partial-fade-left-fast`
- `partial-fade-down-fast`

### Slow speed partial fade

Start wih `0.25` opacity

- `partial-fade-slow`
- `partial-fade-up-slow`
- `partial-fade-right-slow`
- `partial-fade-left-slow`
- `partial-fade-down-slow`

You can add your own animations by following the same pattern in your CSS.

```css
[data-aoe='your-animation'] {
  transform: translateX(-45px);
  transition:
    transform 600ms,
    opacity 900ms;

  &.aoe {
    transform: translateX(0);
    transition-delay: 0s;
  }
}
```

## Properties

| Property name | Type                                         | Default value |
| ------------- | -------------------------------------------- | ------------- |
| `root`        | `Document`, `Element`, `null`, `undefined`   | `null`        |
| `rootMargin`  | `string` in pixel (`px`) or percentage (`%`) | `0px`         |
| `threshold`   | `number`, `number[]` between `0` and `1.0`   | `0.3`         |

`root` is the element that is used as the viewport for checking visibility of the target. Must be the ancestor of the target. Defaults to the browser viewport if not specified or if `null`.

`rootMargin` is the margin around the root. Can have values similar to the CSS margin property, e.g. `"10px 20px 30px 40px"` (top, right, bottom, left). The values can be percentages. Defaults to `'0px'` (no margin).

`threshold` is either a single number or an array of numbers which indicate at what percentage of the target's visibility the observer's callback should be executed. A value of `0.0` or `0` indicates that even a single pixel of the target is visible. A value of `1.0` or `1` indicates that the target is completely visible. Defaults to `0.3` (30%).

---

Inspired by [Animate on Scroll](https://michalsnik.github.io/aos/) and forked from [lliamscholtz/svelte-aoe](https://github.com/lliamscholtz/svelte-aoe)

[npm]: https://www.npmjs.com/package/@castlenine/svelte-aoe
[npm.badge]: https://img.shields.io/npm/v/@castlenine/svelte-aoe
[download]: https://www.npmjs.com/package/@castlenine/svelte-aoe
[download.badge]: https://img.shields.io/npm/d18m/@castlenine/svelte-aoe
