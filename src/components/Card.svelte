<!--
@component
Displays an image card with configurable animate-on-enter attributes.

&nbsp;

@prop aoe {string} ['fade'] - Animation type applied via data-aoe attribute.
@prop aoeThreshold {string} [undefined] - Intersection observer threshold override.
@prop aoeRootMargin {string} [undefined] - Intersection observer root margin override.
@prop aoeRoot {string} [undefined] - Intersection observer root element selector override.
-->
<script lang="ts">
	interface Props {
		aoe?: string;
		aoeThreshold?: string;
		aoeRootMargin?: string;
		aoeRoot?: string;
	}

	const { aoe = 'fade', aoeThreshold, aoeRootMargin, aoeRoot }: Props = $props();
</script>

<section class="text-gray-600 body-font">
	<div class="container mx-auto flex px-5 py-20 md:flex-row flex-col items-center">
		<div class="lg:max-w-lg lg:w-full md:w-1/2 w-5/6 mb-10 md:mb-0">
			<img
				data-aoe={aoe}
				data-aoe-threshold={aoeThreshold}
				data-aoe-root={aoeRoot}
				data-aoe-root-margin={aoeRootMargin}
				class="object-cover object-center rounded"
				src="https://dummyimage.com/720x600"
				alt="Dummy"
				decoding="auto"
				draggable="false" />
		</div>

		<div
			class="lg:flex-grow md:w-1/2 lg:pl-20 md:pl-16 flex flex-col md:items-start md:text-left items-center text-center">
			<code
				class="title-font sm:text-4xl text-3xl mb-4 font-medium text-gray-900 {aoeThreshold || aoeRootMargin || aoeRoot
					? 'bg-emerald-300'
					: 'bg-violet-300'}">
				data-aoe="{aoe}"
			</code>

			<pre class="mb-8 leading-relaxed">
{`<img
	data-aoe="${aoe}"${aoeThreshold == null ? '' : `\n    data-aoe-threshold="${aoeThreshold}"`}${aoeRootMargin == null ? '' : `\n    data-aoe-root-margin="${aoeRootMargin}"`}${aoeRoot == null ? '' : `\n    data-aoe-root="${aoeRoot}"`}
	src="https://dummyimage.com/720x600"
	alt="Dummy"
	decoding="auto"
	draggable="false"
/>`}
			</pre>
		</div>
	</div>
</section>
