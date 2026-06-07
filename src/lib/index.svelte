<script lang="ts">
	import './animations.css';

	import { onMount } from 'svelte';

	export let root: Document | Element | null | undefined = null;
	export let rootMargin: string | undefined = '0px';

	export let threshold: number | number[] | undefined = 0.3;

	onMount(() => {
		const OBSERVER = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					if (entry.isIntersecting) {
						entry.target.classList.add('aoe');
						OBSERVER.unobserve(entry.target);
					}
				});
			},
			{ root, rootMargin, threshold },
		);

		document.querySelectorAll('[data-aoe]').forEach((el) => OBSERVER.observe(el));

		return () => OBSERVER.disconnect();
	});
</script>

<!--
@component
### AnimationOnEnter (AOE)
This component is used to animate elements when they enter the viewport with the help of [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API).

&nbsp;

@param {Document | Element | null | undefined} `root` - The element that is used as the viewport for checking visibility of the target. Must be the ancestor of the target. Defaults to the browser viewport if not specified or if `null`.
@param {string | undefined} `rootMargin` - Margin around the root. Can have values similar to the CSS margin property, e.g. "10px 20px 30px 40px" (top, right, bottom, left). The values can be percentages. Defaults to '0px' (no margin).
@param {number | number[]} `threshold` - Either a single number or an array of numbers which indicate at what percentage of the target's visibility the observer's callback should be executed. A value of 0.0 indicates that even a single pixel of the target is visible. A value of 1.0 indicates that the target is completely visible. Defaults to 0.3 (30%).
-->
