<!--
@component
Mount once in your root layout to observe `[data-aoe]` elements and apply the `aoe` class when they enter the viewport. Dynamically added elements are registered automatically.

&nbsp;

@prop root {Document | Element | null} [null] - Intersection observer root element. Defaults to the browser viewport when null.
@prop rootMargin {string} ['0px'] - Root margin around the observer root (CSS margin syntax).
@prop threshold {number | number[]} [0.3] - Visibility ratio (0–1) at which the observer callback runs.
-->
<script lang="ts">
	import { onMount } from 'svelte';

	import { SvelteMap } from 'svelte/reactivity';

	import './animations.css';

	interface Props {
		root?: Document | Element | null;
		rootMargin?: string;
		threshold?: number | number[];
	}

	const { root = null, rootMargin = '0px', threshold = 0.3 }: Props = $props();

	onMount(() => {
		const DEFAULT_ROOT = root;
		const DEFAULT_ROOT_MARGIN = rootMargin;
		const DEFAULT_THRESHOLD = threshold;

		const OBSERVERS = new SvelteMap<string, IntersectionObserver>();
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
			const PARSED = RAW_THRESHOLD == null ? NaN : parseFloat(RAW_THRESHOLD);
			const THRESHOLD = isNaN(PARSED) ? DEFAULT_THRESHOLD : PARSED;

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
