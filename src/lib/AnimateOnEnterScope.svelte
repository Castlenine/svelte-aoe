<!--
@component
### AnimateOnEnterScope
Convenience wrapper that sets `data-aoe-scope` defaults for all `data-aoe` descendants.
Elements inside the scope inherit these values unless they have their own `data-aoe-*` overrides.

&nbsp;

@param {string | undefined} `root` - CSS selector for the root element used as the viewport.
@param {string | undefined} `rootMargin` - Margin around the root (px or %).
@param {number | undefined} `threshold` - Visibility percentage (0 to 1) to trigger animations.
-->

<script lang="ts">
	export let root: string | undefined = undefined;
	export let rootMargin: string | undefined = undefined;
	export let threshold: number | undefined = undefined;
</script>

<div
	{...$$restProps}
	data-aoe-scope
	data-aoe-root={root}
	data-aoe-root-margin={rootMargin}
	data-aoe-threshold={threshold != null ? String(threshold) : undefined}
>
	<slot />
</div>
