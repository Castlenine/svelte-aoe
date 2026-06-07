<div align="center">

# `@castlenine/svelte-aoe`

[![npm.badge]][npm] [![download.badge]][download] [![contribution.badge]][contribution]

A Svelte component for animating elements, with no dependencies.
</div>

`@castlenine/svelte-aoe` uses the [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to detect when an element enters the viewport.

When the element enters the viewport, `@castlenine/svelte-aoe` applies a class that triggers a CSS animation.

## 🚀 [Demo](https://castlenine-svelte-aoe.vercel.app/)

## Installation

Use your package manager to install:

```shell
npm i @castlenine/svelte-aoe
```

## Setup

1. Import and add the component **once** in your root layout:

File: `src/routes/+layout.svelte`

```svelte
<script>
  import AnimateOnEnter from '@castlenine/svelte-aoe';
</script>

<AnimateOnEnter />

<slot />
```

2. Add a `data-aoe` attribute to any element you want to animate:

```svelte
<img data-aoe="fade-up" src="https://dummyimage.com/500x300"/>
```

Elements are automatically detected, including those added dynamically by page navigation.

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

Starts with `0.25` opacity

- `partial-fade`
- `partial-fade-up`
- `partial-fade-right`
- `partial-fade-left`
- `partial-fade-down`

### Fast speed partial fade

Starts with `0.25` opacity

- `partial-fade-fast`
- `partial-fade-up-fast`
- `partial-fade-right-fast`
- `partial-fade-left-fast`
- `partial-fade-down-fast`

### Slow speed partial fade

Starts with `0.25` opacity

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

| Property name | Type                                          | Default value |
| ------------- | --------------------------------------------- | ------------- |
| `root`        | `Document`, `Element`, `null`, `undefined`    | `null`        |
| `rootMargin`  | `string` in pixels (`px`) or percentage (`%`) | `0px`         |
| `threshold`   | `number`, `number[]` between `0` and `1.0`    | `0.3`         |

`root` is the element used as the viewport for checking the visibility of the target. It must be an ancestor of the target. If not specified or if set to `null`, it defaults to the browser viewport.

`rootMargin` is the margin around the root. It can have values similar to the CSS margin property, e.g., `"10px 20px 30px 40px"` (top, right, bottom, left). The values can be percentages. The default is `'0px'` (no margin).

`threshold` is either a single number or an array of numbers, indicating at what percentage of the target's visibility the observer's callback should be executed. A value of `0.0` or `0` indicates that even a single pixel of the target is visible. A value of `1.0` or `1` requires the target to be completely visible. The default is `0.3` (30%).

## Scoped Overrides

Set defaults for all `data-aoe` elements within a page or section using `data-aoe-scope` on a container element, or the `<AnimateOnEnterScope>` convenience component. Elements inside a scope inherit its values unless they have their own per-element `data-aoe-*` overrides.

### Using the `data-aoe-scope` attribute

```svelte
<div data-aoe-scope data-aoe-threshold="0.5" data-aoe-root-margin="20px">
  <!-- All elements inside use threshold=0.5 and rootMargin=20px -->
  <img data-aoe="fade-up" src="..." />

  <!-- Per-element overrides still take priority -->
  <img data-aoe="fade-up" data-aoe-threshold="0.8" src="..." />
</div>
```

### Using the `<AnimateOnEnterScope>` component

```svelte
<script>
  import { AnimateOnEnterScope } from '@castlenine/svelte-aoe';
</script>

<AnimateOnEnterScope threshold={0.5} rootMargin="20px">
  <img data-aoe="fade-up" src="..." />
</AnimateOnEnterScope>
```

| Property name | Type                                          | Default value |
| ------------- | --------------------------------------------- | ------------- |
| `root`        | CSS selector `string`                         | `undefined`   |
| `rootMargin`  | `string` in pixels (`px`) or percentage (`%`) | `undefined`   |
| `threshold`   | `number` between `0` and `1.0`                | `undefined`   |

Scopes can be nested. The innermost scope takes precedence.

## Per-Element Overrides

Individual elements can override scope or global properties using `data-aoe-*` attributes. If an attribute is not set, the value from the nearest `data-aoe-scope` ancestor is used, then `<AnimateOnEnter />`, and then the package default.

| Attribute              | Type                                          | Overrides    |
| ---------------------- | --------------------------------------------- | ------------ |
| `data-aoe-threshold`   | `number` between `0` and `1.0`                | `threshold`  |
| `data-aoe-root`        | CSS selector `string`                         | `root`       |
| `data-aoe-root-margin` | `string` in pixels (`px`) or percentage (`%`) | `rootMargin` |

### Examples

```svelte
<!-- Uses global defaults -->
<img data-aoe="fade-up" src="..." />

<!-- Overrides threshold only (animates as soon as 1px is visible) -->
<img data-aoe="fade-up" data-aoe-threshold="0" src="..." />

<!-- Overrides rootMargin only (triggers 200px before entering the viewport) -->
<img data-aoe="fade-up" data-aoe-root-margin="200px" src="..." />

<!-- Overrides root to a specific scroll container -->
<img data-aoe="fade-up" data-aoe-root="#scroll-container" src="..." />

<!-- Multiple overrides -->
<img data-aoe="fade-up" data-aoe-threshold="0.8" data-aoe-root-margin="50px" src="..." />
```

---

Inspired by [Animate on Scroll](https://michalsnik.github.io/aos/) and forked from [lliamscholtz/svelte-aoe](https://github.com/lliamscholtz/svelte-aoe)

[npm]: https://www.npmjs.com/package/@castlenine/svelte-aoe
[npm.badge]: https://img.shields.io/npm/v/@castlenine/svelte-aoe
[download]: https://www.npmjs.com/package/@castlenine/svelte-aoe
[download.badge]: https://img.shields.io/npm/d18m/@castlenine/svelte-aoe
[contribution]: https://github.com/Castlenine/svelte-aoe
[contribution.badge]: https://img.shields.io/badge/contributions-welcome-green
