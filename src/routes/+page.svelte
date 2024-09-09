<script>
	import { LucidePlus, Trash2 } from 'lucide-svelte';
	import { ImageUp } from 'lucide-svelte';
	import { RotateCcw } from 'lucide-svelte';
	import { Printer } from 'lucide-svelte';

	import { onMount } from 'svelte';
	import Details from '$lib/components/Details.svelte';

	import TextBox from '$lib/components/TextBox.svelte';

	// Default invoiceData object
	let invoiceData = {
		billedBy: {
			company: '',
			address: {
				apartment: '',
				street: '',
				city: '',
				state: '',
				pincode: ''
			},
			contact: {
				phone: '',
				email: ''
			},
			bankDetails: {
				accountNumber: '',
				accountName: '',
				bankName: ''
			},
			invoiceDetails: {
				number: null,
				created: '',
				due: '',
				tax: null,
				note: ''
			}
		},
		billedTo: {
			company: '',
			address: {
				apartment: '',
				street: '',
				city: '',
				state: '',
				pincode: ''
			},
			contact: {
				phone: '',
				email: ''
			}
		},
		invoiceItems: [
			{
				description: '',
				unitPrice: null,
				quantity: null
			}
		]
	};
	let selectedImage = null;

	function resetInvoiceData() {
		console.log(invoiceData);
		invoiceData = {
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
					number: 2,
					created: 'May 24, 2014',
					due: 'May 24, 2014',
					tax: 18,
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

		selectedImage = null;
		updateLocalStorage();
	}

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
		} else {
			resetInvoiceData();
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
</script>

<svelte:head>
	<title>Billie - No Strings Attached Invoice Generator</title>
	<meta
		name="description"
		content="Create and download invoices instantly, with zero signups or tracking."
	/>
</svelte:head>

<svelte:body on:click={() => updateLocalStorage()} />

<div
	class="print:rotate-0 print:m-0 -rotate-1 max-w-screen-md mx-auto -mt-20 px-6 py-8 flex flex-col space-y-6 font-inter bg-white print:shadow-none shadow-lg"
>
	<div class="flex flex-row justify-between">
		<!-- Company Details ----------------------------------------->

		<div class="flex flex-col gap-4">
			<div>
				{#if selectedImage}
					<div class="flex flex-row items-center print:space-x-0 space-x-4">
						<div class="print:hidden flex flex-col border rounded-xl shadow-md">
							<button class="p-2" on:click={clearImage}>
								<Trash2 />
							</button>
							<button class="p-2" on:click={() => document.getElementById('imageInput').click()}>
								<ImageUp />
								<input
									id="imageInput"
									type="file"
									accept=".png,.jpg,.jpeg,.webp"
									on:change={handleImageChange}
									class="hidden"
								/>
							</button>
						</div>
						<img src={selectedImage} alt="Company Logo" class="max-h-18 w-auto object-cover m-0" />
					</div>
				{:else}
					<!-- Image Picker -->
					<button
						class="print:hidden p-2 flex flex-row gap-2 rounded-lg shadow-md border cursor-pointer"
						on:click={() => document.getElementById('imageInput').click()}
					>
						<ImageUp />
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
				class="font-bold text-2xl"
				type="text"
				bind:value={invoiceData.billedBy.company}
				on:input={() => updateLocalStorage()}
			/>
		</div>

		<!-- Invoice Number & Date ----------------------------------->

		<div class="relative flex flex-col items-end gap-1">
			<h2 class="font-bold text-2xl text-[#1E6F5C] text-right">
				INVOICE :
				<input
					on:input={() => updateLocalStorage()}
					type="text"
					size="2"
					placeholder="2341"
					maxlength="4"
				/>
			</h2>
			<div class="flex flex-col space-y-2 absolute -right-20">
				<button
					on:click={() => {
						resetInvoiceData();
					}}
					class="print:hidden rounded-lg p-2 bg-white shadow cursor-pointer active:scale-90 transition-all duration-100 ease-in"
				>
					<RotateCcw />
				</button>
				<button
					on:click={() => {
						window.print();
					}}
					class="print:hidden rounded-lg p-2 bg-white shadow cursor-pointer active:scale-90 transition-all duration-100 ease-in"
				>
					<Printer />
				</button>
			</div>
			<p>
				Created :
				<input
					on:input={() => updateLocalStorage()}
					type="text"
					size="10"
					placeholder="May 24,2014"
					maxlength="13"
				/>
			</p>
			<p>
				Due :
				<input
					on:input={() => updateLocalStorage()}
					type="text"
					size="10"
					placeholder="May 24,2014"
					maxlength="13"
				/>
			</p>
		</div>
	</div>

	<hr />

	<!-- Address Row ----------------------------------------------------->

	<div class="flex flex-row justify-between">
		<TextBox heading="From" />
		<TextBox heading="To" />
	</div>

	<!-- New Item Form --------------------------------------------------->

	<form class="print:hidden flex flex-row justif-between gap-2">
		<button
			disabled={itemDesc == '' && false}
			on:click={() => {
				addItem();
			}}
			class="absolute -ml-20 print:hidden rounded-full p-2 bg-white shadow cursor-pointer active:scale-90 transition-all duration-100 ease-in"
		>
			<LucidePlus stroke="#557571" />
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

	<!-- Table ----------------------------------------------------------->

	<div class="flex flex-col">
		<div class="flex flex-row bg-[#1e6f5c]">
			<p class="font-bold text-white p-2 border grow break-all">Item Description</p>
			<input
				disabled
				class="p-2 bg-inherit border placeholder:font-bold placeholder:text-white"
				maxlength="7"
				size="6"
				type="text"
				placeholder="Unit Price"
			/>
			<input
				disabled
				class="p-2 bg-inherit border placeholder:font-bold placeholder:text-white"
				maxlength="4"
				size="4"
				type="text"
				placeholder="Qty"
			/>
			<input
				disabled
				class="p-2 bg-inherit border placeholder:font-bold placeholder:text-white"
				maxlength="4"
				size="12"
				type="text"
				placeholder="Total"
			/>
		</div>
		{#each invoiceData.invoiceItems as item, index}
			<div class="flex flex-row even:bg-gray-50 break-all">
				<button
					on:click={() => {
						deleteItem(index);
					}}
					class="absolute -ml-20 print:hidden rounded-full p-2 bg-white shadow cursor-pointer active:scale-90 transition-all duration-100 ease-in"
				>
					<Trash2 color="#C96868" />
				</button>

				<p contenteditable="true" class="p-2 border grow break-all">{item.description}</p>
				<input class="p-2 border" maxlength="7" size="6" type="text" bind:value={item.unitPrice} />
				<input class="p-2 border" maxlength="4" size="4" type="text" bind:value={item.quantity} />
				<input
					disabled
					class="p-2 border"
					maxlength="4"
					size="12"
					type="text"
					placeholder={item.unitPrice * item.quantity}
				/>
			</div>
		{/each}

		<div class="flex flex-row even:bg-gray-50 break-all">
			<p class="p-2 border grow break-all text-right font-bold">Subtotal</p>
			<input
				disabled
				class="p-2 border"
				maxlength="2"
				size="12"
				type="text"
				placeholder={subTotal}
			/>
		</div>
		<div class="flex flex-row even:bg-gray-50 break-all">
			<p class="p-2 border grow break-all text-right font-bold">Tax</p>
			<input
				class="p-2 border"
				maxlength="2"
				size="12"
				type="text"
				bind:value={invoiceData.billedBy.invoiceDetails.tax}
			/>
		</div>
		<div class="flex flex-row even:bg-gray-50 break-all">
			<p class="p-2 border grow break-all text-right font-bold">Total Due</p>
			<input
				disabled
				class="p-2 border"
				maxlength="2"
				size="12"
				type="text"
				placeholder={totalDue}
			/>
		</div>
	</div>

	<!-- Additional Details  --------------------------------------------->

	<TextBox heading="Note" />
	<p>Thank you for your business</p>
	<hr />

	<div class="flex flex-row justify-between">
		<Details
			heading="Payment Info"
			data={[
				{
					label: 'Account',
					value: '',
					placeholder: '987654321098',
					type: 'text', // text, tel, email
					pattern: '.+@example.com',
					minlength: 0,
					maxlength: 12
				},
				{
					label: 'A/C Name',
					value: '',
					placeholder: 'Piper Piper Inc',
					type: 'text',
					pattern: '',
					minlength: 0,
					maxlength: 28
				},
				{
					label: 'Bank',
					value: '',
					placeholder: 'Silicion Valley Bank',
					type: 'text',
					pattern: '',
					minlength: 0,
					maxlength: 28
				}
			]}
		/>

		<Details
			heading="Questions?"
			data={[
				{
					label: 'Email',
					value: '',
					placeholder: 'jared.dunn@piedpiper.com',
					type: 'email', // text, tel, email
					pattern: '.+@example.com',
					minlength: 0,
					maxlength: 28
				},
				{
					label: 'Phone',
					value: '',
					placeholder: '+91 9876543210',
					type: 'tel', // text, tel, email
					pattern: '[0-9]{3}-[0-9]{3}-[0-9]{4}',
					minlength: 0,
					maxlength: 14
				}
			]}
		/>
	</div>
</div>
