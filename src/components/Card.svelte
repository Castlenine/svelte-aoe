<script lang="ts">
	export let aoe = 'fade';
	export let aoeThreshold: string | undefined = undefined;
	export let aoeRootMargin: string | undefined = undefined;
	export let aoeRoot: string | undefined = undefined;
</script>

<section class="text-gray-600 body-font">
	<div class="container mx-auto flex px-5 py-20 md:flex-row flex-col items-center">
		<div class="lg:max-w-lg lg:w-full md:w-1/2 w-5/6 mb-10 md:mb-0">
			<img
				data-aoe={aoe}
				data-aoe-threshold={aoeThreshold}
				data-aoe-root-margin={aoeRootMargin}
				data-aoe-root={aoeRoot}
				class="object-cover object-center rounded"
				alt="Dummy"
				src="https://dummyimage.com/720x600"
			/>
		</div>
		<div class="lg:flex-grow md:w-1/2 lg:pl-20 md:pl-16 flex flex-col md:items-start md:text-left items-center text-center">
			<code
				class="title-font sm:text-4xl text-3xl mb-4 font-medium text-gray-900 {aoeThreshold || aoeRootMargin || aoeRoot ? 'bg-emerald-300' : 'bg-violet-300'}"
			>
				data-aoe="{aoe}"
			</code>
			<pre class="mb-8 leading-relaxed">
{`<img
    data-aoe="${aoe}"${aoeThreshold != null ? `\n    data-aoe-threshold="${aoeThreshold}"` : ''}${aoeRootMargin != null ? `\n    data-aoe-root-margin="${aoeRootMargin}"` : ''}${aoeRoot != null ? `\n    data-aoe-root="${aoeRoot}"` : ''}
    alt="Dummy"
    src="https://dummyimage.com/720x600"
/>`}
			</pre>
		</div>
	</div>
</section>
