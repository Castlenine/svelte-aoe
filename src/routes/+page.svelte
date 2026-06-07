<script lang="ts">
	import { onMount } from 'svelte';

	import AnimateOnEnter from '$lib/index.svelte';
	import { AnimateOnEnterScope } from '$lib/index';

	import Card from '../components/Card.svelte';
	import Pill from '../components/Pill.svelte';

	let rootMargin = $state('0px');
	let threshold = $state(0.3);
	let isReady = $state(false);

	const DIRECTIONS = [
		'up',
		'right',
		'down',
		'left',
		'up-slow',
		'right-slow',
		'down-slow',
		'left-slow',
		'up-fast',
		'right-fast',
		'down-fast',
		'left-fast',
	] as const;

	const FADES = [
		'fade',
		'fade-up',
		'fade-right',
		'fade-down',
		'fade-left',
		'fade-slow',
		'fade-up-slow',
		'fade-right-slow',
		'fade-down-slow',
		'fade-left-slow',
		'fade-fast',
		'fade-up-fast',
		'fade-right-fast',
		'fade-down-fast',
		'fade-left-fast',
		'partial-fade',
		'partial-fade-up',
		'partial-fade-right',
		'partial-fade-down',
		'partial-fade-left',
		'partial-fade-slow',
		'partial-fade-up-slow',
		'partial-fade-right-slow',
		'partial-fade-down-slow',
		'partial-fade-left-slow',
		'partial-fade-fast',
		'partial-fade-up-fast',
		'partial-fade-right-fast',
		'partial-fade-down-fast',
		'partial-fade-left-fast',
	] as const;

	let readyTimeoutId: ReturnType<typeof setTimeout> | undefined;

	function scheduleReady(): void {
		clearTimeout(readyTimeoutId);
		isReady = false;

		readyTimeoutId = setTimeout(() => {
			isReady = true;
		}, 0);
	}

	function handleInputRootMargin(event: Event): void {
		const VALUE = (event.target as HTMLInputElement).value;

		if (VALUE === '') {
			rootMargin = '0px';
		} else {
			rootMargin = VALUE;
		}

		if (!rootMargin.includes('px') && !rootMargin.includes('%')) {
			rootMargin = `${VALUE}px`;
		}

		try {
			window.localStorage.setItem('rootMargin', rootMargin);
		} catch (error) {
			console.error('handleInputRootMargin: Local storage is not available', error);
		}

		scheduleReady();
	}

	function handleInputThreshold(event: Event): void {
		const VALUE = (event.target as HTMLInputElement).value;

		if (VALUE === '') {
			threshold = 0;
		} else {
			threshold = Number(VALUE);
		}

		try {
			window.localStorage.setItem('threshold', String(threshold));
		} catch (error) {
			console.error('handleInputThreshold: Local storage is not available', error);
		}

		scheduleReady();
	}

	function handleResetValue(): void {
		rootMargin = '0px';
		threshold = 0.3;

		scheduleReady();
	}

	onMount(() => {
		try {
			rootMargin = window.localStorage.getItem('rootMargin') ?? '0px';
			threshold = Number(window.localStorage.getItem('threshold')) || 0.3;
		} catch (error) {
			console.error('onMount: Local storage is not available', error);
		}

		isReady = true;

		return () => {
			clearTimeout(readyTimeoutId);
		};
	});
</script>

<svelte:head>
	<title>@castlenine/svelte-aoe</title>
	<meta name="description" content="A Svelte component for animating elements, with no dependencies." />
</svelte:head>

{#if isReady}
	<AnimateOnEnter {rootMargin} {threshold} />

	<main>
		<section class="text-gray-600 body-font">
			<div class="container px-5 pt-28 mx-auto">
				<a
					class="sm:text-5xl text-4xl font-medium title-font text-gray-900 mb-10 inline-block hover:opacity-70 underline"
					href="https://www.npmjs.com/package/@castlenine/svelte-aoe"
					target="_blank">@castlenine/svelte-aoe</a>
				<h2 class="sm:text-4xl text-3xl font-medium title-font mb-4 text-gray-900">Change values:</h2>
				<div class="flex flex-col gap-4">
					<div>
						<label for="root-margin" class="font-medium">Root margin:</label>
						<input
							id="root-margin"
							class="border p-1"
							value={rootMargin}
							type="text"
							onchange={handleInputRootMargin} />
					</div>
					<div>
						<label for="threshold" class="font-medium">Threshold:</label>
						<input
							id="threshold"
							class="border p-1"
							value={threshold}
							type="number"
							min="0"
							max="1.0"
							step="0.01"
							onchange={handleInputThreshold} />
					</div>
					<div>
						<button class="bg-violet-800 rounded px-4 py-1 text-zinc-50 mt-2" type="button" onclick={handleResetValue}
							>Reset</button>
					</div>
				</div>
				<div class="flex flex-wrap w-full mb-2 mt-10">
					<div class="lg:w-1/2 w-full mb-2 lg:mb-0">
						<h2 class="sm:text-3xl text-2xl font-medium title-font mb-2 text-gray-900">Directions</h2>
						<div class="h-1 w-20 bg-violet-500 rounded"></div>
					</div>
					<div class="lg:w-1/2 w-full leading-relaxed text-gray-500">
						{#each DIRECTIONS as direction (direction)}
							<Pill label={direction} />
						{/each}
					</div>
				</div>
			</div>
		</section>
		{#each DIRECTIONS as direction (direction)}
			<Card aoe={direction} />
		{/each}

		<section class="text-gray-600 body-font">
			<div class="container px-5 pt-28 mx-auto">
				<div class="flex flex-wrap w-full mb-2">
					<div class="lg:w-1/2 w-full mb-2 lg:mb-0">
						<h2 class="sm:text-3xl text-2xl font-medium title-font mb-2 text-gray-900">Fades</h2>
						<div class="h-1 w-20 bg-violet-500 rounded"></div>
					</div>
					<div class="lg:w-1/2 w-full leading-relaxed text-gray-500">
						{#each FADES as fade (fade)}
							<Pill label={fade} />
						{/each}
					</div>
				</div>
			</div>
		</section>
		{#each FADES as fade (fade)}
			<Card aoe={fade} />
		{/each}

		<section class="text-gray-600 body-font">
			<div class="container px-5 pt-28 mx-auto">
				<div class="flex flex-wrap w-full mb-2">
					<div class="lg:w-1/2 w-full mb-2 lg:mb-0">
						<h2 class="sm:text-3xl text-2xl font-medium title-font mb-2 text-gray-900">Per-Element Overrides</h2>
						<div class="h-1 w-20 bg-emerald-500 rounded"></div>
					</div>
					<div class="lg:w-1/2 w-full leading-relaxed text-gray-500">
						<Pill label="data-aoe-threshold" />
						<Pill label="data-aoe-root-margin" />
						<Pill label="data-aoe-root" />
					</div>
				</div>
			</div>
		</section>
		<Card aoe="fade-up" aoeThreshold="0" />
		<Card aoe="fade-up" aoeThreshold="1" />
		<Card aoe="fade-up" aoeRootMargin="200px" />

		<section class="text-gray-600 body-font">
			<div class="container px-5 pt-28 mx-auto">
				<div class="flex flex-wrap w-full mb-2">
					<div class="lg:w-1/2 w-full mb-2 lg:mb-0">
						<h2 class="sm:text-3xl text-2xl font-medium title-font mb-2 text-gray-900">Scoped Overrides</h2>
						<div class="h-1 w-20 bg-amber-500 rounded"></div>
					</div>
					<div class="lg:w-1/2 w-full leading-relaxed text-gray-500">
						<Pill label="data-aoe-scope" />
						<Pill label="AnimateOnEnterScope" />
					</div>
				</div>
			</div>
		</section>
		<AnimateOnEnterScope class="container mx-auto px-5" threshold={0}>
			<p class="text-sm text-gray-500 mb-4 bg-amber-100 p-2 rounded">
				Inside <code>&lt;AnimateOnEnterScope threshold=&#123;0&#125;&gt;</code> — all elements animate as soon as 1px is visible.
			</p>
			<Card aoe="fade-left" />
			<Card aoe="fade-right" />
		</AnimateOnEnterScope>
	</main>
{/if}
