<!--
@component
### AnimateOnEnter (AOE)
This component is used to animate elements when they enter the viewport with the help of [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API).

Place this component once in your root layout. Elements with `data-aoe` attributes will be automatically detected, including dynamically added elements from page navigation.

#### Configuration cascade
1. Package defaults: `root: null`, `rootMargin: '0px'`, `threshold: 0.3`
2. Component props: override package defaults globally
3. `data-aoe-scope` on a container: override component props for a page or section
4. Per-element `data-aoe-*` attributes: override scope or component props for individual elements

&nbsp;

@param {Document | Element | null | undefined} `root` - The element that is used as the viewport for checking visibility of the target. Must be the ancestor of the target. Defaults to the browser viewport if not specified or if `null`.
@param {string | undefined} `rootMargin` - Margin around the root. Can have values similar to the CSS margin property, e.g. "10px 20px 30px 40px" (top, right, bottom, left). The values can be percentages. Defaults to '0px' (no margin).
@param {number | number[]} `threshold` - Either a single number or an array of numbers which indicate at what percentage of the target's visibility the observer's callback should be executed. A value of 0.0 indicates that even a single pixel of the target is visible. A value of 1.0 indicates that the target is completely visible. Defaults to 0.3 (30%).
-->

<script lang="ts">
	import './animations.css';

	import { onMount } from 'svelte';

	export let root: Document | Element | null | undefined = null;
	export let rootMargin: string | undefined = '0px';
	export let threshold: number | number[] | undefined = 0.3;

	onMount(() => {
		const DEFAULT_ROOT = (root ?? null);
		const DEFAULT_ROOT_MARGIN = rootMargin ?? '0px';
		const DEFAULT_THRESHOLD = threshold ?? 0.3;

		const OBSERVERS = new Map<string, IntersectionObserver>();
		const REGISTERED = new WeakSet<Element>();

		function createObserver(options: IntersectionObserverInit): IntersectionObserver {
			const OBSERVER = new IntersectionObserver((entries) => {
				for (const ENTRY of entries) {
					if (ENTRY.isIntersecting) {
						ENTRY.target.classList.add('aoe');
						OBSERVER.unobserve(ENTRY.target);
					}
				}
			}, options);
			return OBSERVER;
		}

		function configKey(rootSelector: string | null, rm: string, th: number | number[]): string {
			const TH_STR = Array.isArray(th) ? th.join(',') : String(th);
			return `${rootSelector ?? 'null'}|${rm}|${TH_STR}`;
		}

		const DEFAULT_KEY = configKey(null, DEFAULT_ROOT_MARGIN, DEFAULT_THRESHOLD);
		const DEFAULT_OBSERVER = createObserver({
			root: DEFAULT_ROOT,
			rootMargin: DEFAULT_ROOT_MARGIN,
			threshold: DEFAULT_THRESHOLD,
		});
		OBSERVERS.set(DEFAULT_KEY, DEFAULT_OBSERVER);

		function getOrCreateObserver(key: string, options: IntersectionObserverInit): IntersectionObserver {
			let observer = OBSERVERS.get(key);
			if (!observer) {
				observer = createObserver(options);
				OBSERVERS.set(key, observer);
			}
			return observer;
		}

		function resolveConfig(el: Element): {
			rootSelector: string | null;
			rootMargin: string;
			threshold: number | number[];
		} {
			const ROOT_ATTR = el.getAttribute('data-aoe-root');
			const ROOT_MARGIN_ATTR = el.getAttribute('data-aoe-root-margin');
			const THRESHOLD_ATTR = el.getAttribute('data-aoe-threshold');

			const SCOPE = el.closest('[data-aoe-scope]');

			const ROOT_SELECTOR = ROOT_ATTR ?? SCOPE?.getAttribute('data-aoe-root') ?? null;
			const ROOT_MARGIN = ROOT_MARGIN_ATTR ?? SCOPE?.getAttribute('data-aoe-root-margin') ?? DEFAULT_ROOT_MARGIN;

			const RAW_THRESHOLD = THRESHOLD_ATTR ?? SCOPE?.getAttribute('data-aoe-threshold');
			const PARSED = RAW_THRESHOLD != null ? parseFloat(RAW_THRESHOLD) : NaN;
			const THRESHOLD = !isNaN(PARSED) ? PARSED : DEFAULT_THRESHOLD;

			return { rootSelector: ROOT_SELECTOR, rootMargin: ROOT_MARGIN, threshold: THRESHOLD };
		}

		function registerElement(el: Element): void {
			if (REGISTERED.has(el) || el.classList.contains('aoe')) return;
			REGISTERED.add(el);

			const { rootSelector: ROOT_SELECTOR, rootMargin: ROOT_MARGIN, threshold: THRESHOLD } = resolveConfig(el);

			if (!ROOT_SELECTOR && ROOT_MARGIN === DEFAULT_ROOT_MARGIN && THRESHOLD === DEFAULT_THRESHOLD) {
				DEFAULT_OBSERVER.observe(el);
				return;
			}

			const EL_ROOT = ROOT_SELECTOR ? document.querySelector(ROOT_SELECTOR) : DEFAULT_ROOT;
			const KEY = configKey(ROOT_SELECTOR, ROOT_MARGIN, THRESHOLD);
			const OBSERVER = getOrCreateObserver(KEY, {
				root: EL_ROOT,
				rootMargin: ROOT_MARGIN,
				threshold: THRESHOLD,
			});
			OBSERVER.observe(el);
		}

		document.querySelectorAll('[data-aoe]').forEach((el) => registerElement(el));

		const MUTATION_OBSERVER = new MutationObserver((mutations) => {
			for (const MUTATION of mutations) {
				for (const NODE of MUTATION.addedNodes) {
					if (NODE.nodeType !== Node.ELEMENT_NODE) continue;
					const el = NODE as Element;
					if (el.hasAttribute('data-aoe')) registerElement(el);
					el.querySelectorAll('[data-aoe]').forEach((child) => registerElement(child));
				}
			}
		});
		MUTATION_OBSERVER.observe(document.body, { childList: true, subtree: true });

		return () => {
			MUTATION_OBSERVER.disconnect();
			for (const OBSERVER of OBSERVERS.values()) {
				OBSERVER.disconnect();
			}
			OBSERVERS.clear();
		};
	});
</script>
