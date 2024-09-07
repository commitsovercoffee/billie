<script>
	let mail = 'abc@gmail.com';
	let phone = '+91 9876543210';

	let account = '1234 567 890';
	let accountName = 'Jared Dunn';
	let bankName = 'Silicon Vallery Bank';

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

	let note = 'Add comments here.';

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

	let selectedImage = null;

	function handleImageChange(event) {
		const file = event.target.files[0];

		if (
			file &&
			(file.type === 'image/png' || file.type === 'image/jpeg' || file.type === 'image/webp')
		) {
			const reader = new FileReader();
			reader.onload = () => {
				selectedImage = reader.result;
			};
			reader.readAsDataURL(file);
		} else {
			alert('Please select a valid image (.png, .jpg, or .webp)');
		}
	}

	function clearImage() {
		selectedImage = null;
	}
</script>

<div class="prose prose-sm max-w-screen-md mx-auto my-6 px-6 py-8 shadow flex flex-col space-y-8">
	<div class="flex flex-row justify-between">
		<div>
			<div>
				<!-- Display Image -->
				{#if selectedImage}
					<div class="flex flex-row justify-between items-center">
						<div class="flex flex-col rounded-xl shadow-md">
							<button on:click={clearImage}>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									width="24"
									height="24"
									viewBox="0 0 24 24"
									fill="none"
									stroke="currentColor"
									stroke-width="2"
									stroke-linecap="round"
									stroke-linejoin="round"
									class="m-4 cursor-pointer lucide lucide-trash-2"
									><path d="M3 6h18" /><path d="M19 6v14c0 1-1 2-2 2H7c-1 0-2-1-2-2V6" /><path
										d="M8 6V4c0-1 1-2 2-2h4c1 0 2 1 2 2v2"
									/><line x1="10" x2="10" y1="11" y2="17" /><line
										x1="14"
										x2="14"
										y1="11"
										y2="17"
									/></svg
								>
							</button>
							<button on:click={() => document.getElementById('imageInput').click()}>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									width="24"
									height="24"
									viewBox="0 0 24 24"
									fill="none"
									stroke="currentColor"
									stroke-width="2"
									stroke-linecap="round"
									stroke-linejoin="round"
									class="m-4 cursor-pointer lucide lucide-image-up"
									><path
										d="M10.3 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2v10l-3.1-3.1a2 2 0 0 0-2.814.014L6 21"
									/><path d="m14 19.5 3-3 3 3" /><path d="M17 22v-5.5" /><circle
										cx="9"
										cy="9"
										r="2"
									/></svg
								>
							</button>
						</div>
						<img src={selectedImage} alt="Company Logo" class="h-24 w-auto object-fill m-0" />
					</div>
				{:else}
					<button
						class="flex flex-row space-x-2 items-center border border-dashed border-gray-400 px-4 py-1 rounded-lg cursor-pointer hover:bg-gray-50 transition duration-150 ease-in-out"
						on:click={() => document.getElementById('imageInput').click()}
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							width="24"
							height="24"
							viewBox="0 0 24 24"
							fill="none"
							stroke="currentColor"
							stroke-width="2"
							stroke-linecap="round"
							stroke-linejoin="round"
							class="lucide lucide-image-up"
							><path
								d="M10.3 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2v10l-3.1-3.1a2 2 0 0 0-2.814.014L6 21"
							/><path d="m14 19.5 3-3 3 3" /><path d="M17 22v-5.5" /><circle
								cx="9"
								cy="9"
								r="2"
							/></svg
						>

						<p class="text-center text-gray-600">Click to select an image</p>
					</button>
					<input
						id="imageInput"
						type="file"
						accept=".png,.jpg,.jpeg,.webp"
						on:change={handleImageChange}
						class="hidden"
					/>
				{/if}
			</div>
			<p class="font-bold text-2xl">Company Name</p>
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

	<div class="flex flex-row justify-between">
		<span
			class="p-2 w-60 h-auto break-words text-pretty shadow-inner"
			contenteditable="true"
			bind:innerText={note}
		></span>
		<div class="flex flex-col gap-2">
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
				<input
					type="number"
					bind:value={tax}
					placeholder="Tax %"
					class="border rounded-l p-2 w-32"
				/>
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
	</div>

	<p>Thank you for your business</p>

	<hr />

	<div class="flex flex-row justify-between">
		<div>
			<h4 class="font-bold my-4">Questions</h4>
			<p>Email : <span contenteditable="true" bind:innerText={mail}></span></p>
			<p>Phone : <span contenteditable="true" bind:innerText={phone}></span></p>
		</div>
		<div>
			<h4 class="font-bold my-4">Payment Info</h4>
			<p>Account : <span contenteditable="true" bind:innerText={account}></span></p>
			<p>A/C Name : <span contenteditable="true" bind:innerText={accountName}></span></p>
			<p>Bank : <span contenteditable="true" bind:innerText={bankName}></span></p>
		</div>
	</div>
</div>
