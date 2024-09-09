<script>
	export let label = 'Label';
	export let disabled = false;
	export let placeholder = '[meow]';
	export let value = '';
</script>

<div class="flex flex-row even:bg-gray-50 break-all">
	<p class="p-2 border grow break-all text-right font-bold">{label}</p>
	<input
		{disabled}
		class="p-2 border"
		maxlength="2"
		size="12"
		type="text"
		{placeholder}
		bind:value
	/>
</div>
