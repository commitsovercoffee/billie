<script>
	export let label = 'Feature Item';
</script>

<div class="m-2 p-2 flex flex-row gap-2">
	<slot />
	<p class="opacity-60">{label}</p>
</div>
