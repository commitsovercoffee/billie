<script>
	import { onMount } from 'svelte';

	// Default invoiceData object
	let invoiceData = {
		billedBy: {
			company: 'Piper Piper',
			address: {
				apartment: 'Apartment 3B',
				street: '555 Silicon Street',
				city: 'Palo Alto',
				state: 'California',
				pincode: '94301'
			},
			contact: {
				phone: '+1 650-555-1234',
				email: 'jared.dunn@piedpiper.com'
			},
			bankDetails: {
				accountNumber: '9876 5432 1098',
				accountName: 'Piped Piper Inc',
				bankName: 'Silicon Valley Bank'
			},
			invoiceDetails: {
				date: '2024-09-07',
				tax: 0.18,
				note: "Please note: Due to a mishap with Russ's bottle being on delete, some files were accidentally removed."
			}
		},
		billedTo: {
			company: 'Intersite',
			address: {
				apartment: 'Suite 69',
				street: 'Adult Entertainment',
				city: 'Los Angeles',
				state: 'California',
				pincode: '90028'
			},
			contact: {
				phone: '+1 213-555-6969',
				email: 'billing@intersite.com'
			}
		},
		invoiceItems: [
			{
				description: 'Data Compression Service Subscription',
				unitPrice: 500,
				quantity: 1
			},
			{
				description: 'Cloud Storage (100GB)',
				unitPrice: 100,
				quantity: 5
			},
			{
				description: 'Video Encoding & Streaming Optimization',
				unitPrice: 300,
				quantity: 3
			}
		]
	};

	let selectedImage = null;

	// Function to update local storage whenever invoiceData changes
	function updateLocalStorage() {
		localStorage.setItem('invoiceData', JSON.stringify(invoiceData));
		if (selectedImage) {
			localStorage.setItem('selectedImage', selectedImage); // Save selected image
		}
	}

	onMount(() => {
		// Load data from local storage when the component mounts
		const savedData = localStorage.getItem('invoiceData');
		if (savedData) {
			invoiceData = JSON.parse(savedData); // Parse saved data
		}

		const savedImage = localStorage.getItem('selectedImage');
		if (savedImage) {
			selectedImage = savedImage; // Load saved image
		}
	});

	let itemDesc = '';
	let itemPrice = 1;
	let itemQty = 1;
	$: itemTotal = itemPrice * itemQty;

	$: subTotal = invoiceData.invoiceItems.reduce((total, item) => {
		return total + item.unitPrice * item.quantity;
	}, 0);
	$: totalDue = subTotal + invoiceData.billedBy.invoiceDetails.tax * subTotal;

	function addItem() {
		if (itemDesc != '' && itemPrice > 0 && itemQty > 0) {
			invoiceData.invoiceItems = [
				...invoiceData.invoiceItems,
				{
					description: itemDesc,
					unitPrice: itemPrice,
					quantity: itemQty
				}
			];

			itemDesc = '';
			itemPrice = 1;
			itemQty = 1;

			document.getElementById('descBox').focus();

			updateLocalStorage();
		}
	}

	function deleteItem(index) {
		invoiceData.invoiceItems = invoiceData.invoiceItems.filter((_, i) => i !== index);
		updateLocalStorage();
	}

	function handleImageChange(event) {
		const file = event.target.files[0];

		if (
			file &&
			(file.type === 'image/png' || file.type === 'image/jpeg' || file.type === 'image/webp')
		) {
			const reader = new FileReader();
			reader.onload = () => {
				selectedImage = reader.result;
				updateLocalStorage();
			};
			reader.readAsDataURL(file);
		} else {
			alert('Please select a valid image (.png, .jpg, or .webp)');
		}
	}

	function clearImage() {
		selectedImage = null;
	}

	const tableItemStyle = 'border px-2 py-4 break-words text-pretty';
</script>

<svelte:body on:click={() => updateLocalStorage()} />

<div
	class="-rotate-2 max-w-screen-md mx-auto my-16 px-6 py-8 flex flex-col space-y-6 font-inter bg-white shadow-lg"
>
	<!-- Company & Invoice Row ------------------------------------------->
	<div class="flex flex-row justify-between">
		<!-- Left Section --->
		<div class="flex flex-col gap-4">
			<div>
				<!-- Company Logo -->
				{#if selectedImage}
					<div class="flex flex-row items-center space-x-4">
						<div class="flex flex-col border rounded-xl shadow-md">
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
								<input
									id="imageInput"
									type="file"
									accept=".png,.jpg,.jpeg,.webp"
									on:change={handleImageChange}
									class="hidden"
								/>
							</button>
						</div>
						<img src={selectedImage} alt="Company Logo" class="max-h-32 w-auto object-cover m-0" />
					</div>
				{:else}
					<!-- Image Picker -->
					<button
						class="p-2 flex flex-row gap-2 rounded-lg shadow-md border cursor-pointer"
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
						<p>Click to select an image for logo</p>
						<input
							id="imageInput"
							type="file"
							accept=".png,.jpg,.jpeg,.webp"
							on:change={handleImageChange}
							class="hidden"
						/>
					</button>
				{/if}
			</div>
			<input
				class="font-bold text-2xl text-[#1E6F5C]"
				type="text"
				bind:value={invoiceData.billedBy.company}
				on:input={() => updateLocalStorage()}
			/>
		</div>

		<!-- Right Section -->
		<div>
			<h2 class="font-bold text-2xl text-[#1E6F5C]">INVOICE</h2>
			<input
				type="text"
				placeholder="May 24, 2014"
				contenteditable="true"
				class="w-32"
				maxlength="12"
				on:input={() => updateLocalStorage()}
				bind:value={invoiceData.billedBy.invoiceDetails.date}
			/>
		</div>
	</div>

	<hr />

	<!-- Address Row ----------------------------------------------------->
	<div class="flex flex-row justify-between">
		<div>
			<p class="font-bold">Address :</p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedBy.company}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedBy.address.apartment}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedBy.address.street}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedBy.address.city}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedBy.address.state}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedBy.address.pincode}
			></p>
		</div>
		<div>
			<p class="font-bold">To :</p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedTo.company}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedTo.address.apartment}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedTo.address.street}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedTo.address.city}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedTo.address.state}
			></p>
			<p
				on:input={() => updateLocalStorage()}
				contenteditable="true"
				bind:innerText={invoiceData.billedTo.address.pincode}
			></p>
		</div>
	</div>

	<!-- Bill Details Row ------------------------------------------------>

	<form class="flex flex-row justif-between gap-2">
		<button
			disabled={itemDesc == ''}
			on:click={() => {
				addItem();
			}}
			class="rounded-l-lg p-2 bg-[#C9D990] absolute -ml-16 shadow"
		>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				width="24"
				height="24"
				viewBox="0 0 24 24"
				fill="none"
				stroke="#1C471F"
				stroke-width="2"
				stroke-linecap="round"
				stroke-linejoin="round"
				class="lucide lucide-plus"><path d="M5 12h14" /><path d="M12 5v14" /></svg
			>
		</button>

		<input
			id="descBox"
			type="text"
			bind:value={itemDesc}
			placeholder="Item description"
			class="border rounded-l p-2 grow"
			on:keypress={(e) => {
				e.key == 'Enter' && addItem();
			}}
		/>
		<input
			type="number"
			bind:value={itemPrice}
			placeholder="Unit Price"
			min="1"
			class="border rounded-l p-2 w-32"
			on:keypress={(e) => {
				e.key == 'Enter' && addItem();
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
				e.key == 'Enter' && addItem();
			}}
		/>
		<p class="border rounded-l p-2 w-32">
			{itemTotal}
		</p>
	</form>

	<table class="table-auto realtive">
		<thead class="text-base bg-[#1E6F5C] text-white">
			<tr>
				<th>Item Description</th>
				<th>Unit Price</th>
				<th>Qty</th>
				<th>Total</th>
			</tr>
		</thead>
		<tbody>
			{#each invoiceData.invoiceItems as item, index}
				<button
					on:click={() => {
						deleteItem(index);
					}}
					class="rounded-l-lg p-2 bg-[#F9A474] absolute -ml-16 mt-2 shadow"
				>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						width="24"
						height="24"
						viewBox="0 0 24 24"
						fill="none"
						stroke="#5B2715"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="cursor-pointer lucide lucide-minus"><path d="M5 12h14" /></svg
					>
				</button>
				<tr class="my-2">
					<td
						on:keydown={(e) => {
							e.key == 'Delete' && deleteItem(index);
						}}
						class={tableItemStyle}
						contenteditable="true"
						bind:innerText={item.description}
					>
					</td>
					<td
						on:keydown={(e) => {
							e.key == 'Delete' && deleteItem(index);
						}}
						class="{tableItemStyle} text-right"
						contenteditable="true"
						bind:innerText={item.unitPrice}
					></td>
					<td
						on:keydown={(e) => {
							e.key == 'Delete' && deleteItem(index);
						}}
						class="{tableItemStyle} text-center"
						contenteditable="true"
						bind:innerText={item.quantity}
					></td>
					<td
						on:keydown={(e) => {
							e.key == 'Delete' && deleteItem(index);
						}}
						class="{tableItemStyle} text-right">{item.unitPrice * item.quantity}</td
					>
				</tr>
			{/each}
		</tbody><tfoot>
			<tr>
				<th class="{tableItemStyle} text-right" scope="row" colspan="3">Subtotal</th>
				<td class="{tableItemStyle} text-right">{subTotal}</td>
			</tr>
			<tr>
				<th class="{tableItemStyle} text-right" scope="row" colspan="3">Tax</th>
				<td
					class="{tableItemStyle} text-right"
					contenteditable="true"
					on:input={() => updateCart()}
					bind:innerText={invoiceData.billedBy.invoiceDetails.tax}
				></td>
			</tr>
			<tr>
				<th class="{tableItemStyle} text-right" scope="row" colspan="3">Total Due</th>
				<td class="{tableItemStyle} text-right">{totalDue}</td>
			</tr>
		</tfoot>
	</table>

	<div class="flex flex-row justify-between">
		<span
			class="p-2 w-60 h-auto break-words text-pretty shadow-inner border bg-gray-50"
			contenteditable="true"
			bind:innerText={invoiceData.billedBy.invoiceDetails.note}
			on:input={() => updateLocalStorage()}
		></span>
	</div>

	<p>Thank you for your business</p>

	<hr />

	<div class="flex flex-row justify-between">
		<div>
			<h4 class="font-bold my-4">Payment Info</h4>
			<p>
				Account : <span
					contenteditable="true"
					on:input={() => updateLocalStorage()}
					bind:innerText={invoiceData.billedBy.bankDetails.accountNumber}
				></span>
			</p>
			<p>
				A/C Name : <span
					contenteditable="true"
					on:input={() => updateLocalStorage()}
					bind:innerText={invoiceData.billedBy.bankDetails.accountName}
				></span>
			</p>
			<p>
				Bank : <span
					contenteditable="true"
					on:input={() => updateLocalStorage()}
					bind:innerText={invoiceData.billedBy.bankDetails.bankName}
				></span>
			</p>
		</div>
		<div>
			<h4 class="font-bold my-4">Questions?</h4>
			<p>
				Email : <span
					on:input={() => updateLocalStorage()}
					contenteditable="true"
					bind:innerText={invoiceData.billedBy.contact.email}
				></span>
			</p>
			<p>
				Phone : <span
					on:input={() => updateLocalStorage()}
					contenteditable="true"
					bind:innerText={invoiceData.billedBy.contact.phone}
				></span>
			</p>
		</div>
	</div>
</div>
