<script>
	let itemDesc = '';
	let itemPrice = 1;
	let itemQty = 1;
	$: itemTotal = itemPrice * itemQty;

	let descriptions = [];
	let prices = [];
	let quantities = [];

	let subTotal = 0;
	let tax = 0.18;
	$: totalDue = subTotal + tax * subTotal;

	// Function to add values to arrays
	function addValues() {
		if (itemDesc != '' && itemPrice > 0 && itemQty > 0) {
			descriptions = [...descriptions, itemDesc];
			prices = [...prices, itemPrice];
			quantities = [...quantities, itemQty];

			subTotal = subTotal + itemPrice * itemQty;

			itemDesc = '';
			itemPrice = 1;
			itemQty = 1;

			document.getElementById('descBox').focus();
		}
	}
</script>

<div class=" max-w-screen-md mx-auto m-4 p-4 shadow flex flex-col space-y-8">
	<div class="flex flex-row justify-between">
		<div>
			<img class="h-28 w-auto" src="logo.png" alt="" />
			<h2 class="font-bold text-2xl">Company Name</h2>
		</div>
		<div>
			<h2 class="font-bold text-2xl">INVOICE</h2>
			<p>December 26, 2024</p>
		</div>
	</div>
	<div class="flex flex-row justify-between">
		<div>
			<p class="font-bold">Address</p>
			<p>Apartment, Suite, Unit etc</p>
			<p>Street Name</p>
			<p>City, State</p>
			<p>Pincode</p>
			<p>+91 9876543210</p>
		</div>
		<div>
			<p class="font-bold">To</p>
			<p>Apartment, Suite, Unit etc</p>
			<p>Street Name</p>
			<p>City, State</p>
			<p>Pincode</p>
		</div>
	</div>

	<div class="flex flex-row justif-between gap-2">
		<p class="p-2 grow">Item Description</p>
		<p class="p-2 w-32">Unit Price</p>
		<p class="p-2 w-32">Qnt</p>
		<p class="p-2 w-32">Total</p>
	</div>

	<form class="flex flex-row justif-between gap-2">
		<input
			id="descBox"
			type="text"
			bind:value={itemDesc}
			placeholder="Item description"
			class="border rounded-l p-2 grow"
			on:keypress={(e) => {
				e.key == 'Enter' && addValues();
			}}
		/>
		<input
			type="number"
			bind:value={itemPrice}
			placeholder="Unit Price"
			min="1"
			class="border rounded-l p-2 w-32"
			on:keypress={(e) => {
				e.key == 'Enter' && addValues();
			}}
		/>
		<input
			type="number"
			bind:value={itemQty}
			placeholder="Unit Price"
			min="1"
			step="1"
			class="border rounded-l p-2 w-32"
			on:keypress={(e) => {
				e.key == 'Enter' && addValues();
			}}
		/>
		<p class="border rounded-l p-2 w-32">
			{itemTotal}
		</p>
	</form>

	<div class="flex flex-col gap-2">
		{#each descriptions as _, index}
			<div class="flex flex-row justify-between gap-2">
				<input
					type="text"
					placeholder={descriptions[index]}
					bind:value={descriptions[index]}
					contenteditable="true"
					class="border rounded-l p-2 grow"
				/>
				<input
					type="number"
					min="1"
					placeholder={prices[index]}
					bind:value={prices[index]}
					contenteditable="true"
					class="border rounded-l p-2 w-32"
				/>
				<input
					type="number"
					min="1"
					step="1"
					placeholder={quantities[index]}
					bind:value={quantities[index]}
					contenteditable="true"
					class="border rounded-l p-2 w-32"
				/>

				<p contenteditable="true" class="border rounded-l p-2 w-32">
					{prices[index] * quantities[index]}
				</p>
			</div>
		{/each}
	</div>

	<div class="flex flex-row gap-4 justify-end items-center">
		<p>Subtotal</p>
		<input
			type="text"
			disabled
			bind:value={subTotal}
			placeholder="Tax %"
			class="border rounded-l p-2 w-32"
		/>
	</div>
	<div class="flex flex-row gap-4 justify-end items-center">
		<p>Tax</p>
		<input type="number" bind:value={tax} placeholder="Tax %" class="border rounded-l p-2 w-32" />
	</div>
	<div class="flex flex-row gap-4 justify-end items-center">
		<p>Total Due</p>
		<input
			type="text"
			disabled
			bind:value={totalDue}
			placeholder="Tax %"
			class="border rounded-l p-2 w-32"
		/>
	</div>
</div>
