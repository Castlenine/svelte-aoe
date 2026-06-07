<!--
@component
Displays a styled pill/badge with a text label.

&nbsp;

@prop label {string} [''] - Text content displayed inside the pill.
-->
<script lang="ts">
	interface Props {
		label?: string;
	}

	const { label = '' }: Props = $props();
</script>

<span class="inline-block py-1 px-2 rounded bg-violet-50 text-violet-500 text-md font-medium tracking-widest mr-2">
	{label}
</span>
