<!--
@component
Wraps children in a `[data-aoe-scope]` container so nested `[data-aoe]` elements inherit its intersection observer defaults. Per-element `data-aoe-*` attributes still override scope values. Scopes can be nested; the innermost scope wins.

&nbsp;

@prop root {string} [undefined] - CSS selector for the intersection observer root, mapped to `data-aoe-root`.
@prop rootMargin {string} [undefined] - Root margin override, mapped to `data-aoe-root-margin`.
@prop threshold {number} [undefined] - Threshold override, mapped to `data-aoe-threshold`.
@prop children {Snippet} [undefined] - Default slot content.
-->
<script lang="ts">
	import type { HTMLAttributes } from 'svelte/elements';
	import type { Snippet } from 'svelte';

	interface Props extends HTMLAttributes<HTMLDivElement> {
		root?: string;
		rootMargin?: string;
		threshold?: number;
		children?: Snippet;
	}

	const { root, rootMargin, threshold, children, ...rest }: Props = $props();
</script>

<div
	{...rest}
	data-aoe-scope
	data-aoe-root={root}
	data-aoe-root-margin={rootMargin}
	data-aoe-threshold={threshold == null ? undefined : String(threshold)}>
	{#if children}
		{@render children()}
	{/if}
</div>
