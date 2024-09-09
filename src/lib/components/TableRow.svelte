<script>
	export let desc = '';
	export let unitPrice = '';
	export let quantity = '';
</script>

<p contenteditable="true" class="p-2 border grow break-all">{desc}</p>
<input class="p-2 border" maxlength="7" size="6" type="text" bind:value={unitPrice} />
<input class="p-2 border" maxlength="4" size="4" type="text" bind:value={quantity} />
<input
	disabled
	class="p-2 border"
	maxlength="4"
	size="9"
	type="text"
	placeholder={unitPrice * quantity}
/>
