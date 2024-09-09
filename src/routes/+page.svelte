<script>
	import { LucidePlus, Trash2, ImageUp, RotateCcw, Printer } from 'lucide-svelte';
	import { onMount } from 'svelte';

	let appState = {
		company: { name: '', logo: '' },
		invoice: { number: '', created: '', due: '', from: '', to: '' },
		items: [{ desc: '', price: '', quantity: '' }],
		tax: '',
		note: '',
		payment: { accountNumber: '', accountName: '', bank: '' },
		contact: { mail: '', phone: '' }
	};

	// Utils --------------------------------------------------------------

	function handleImageChange(event) {
		const file = event.target.files[0];

		if (
			file &&
			(file.type === 'image/png' || file.type === 'image/jpeg' || file.type === 'image/webp')
		) {
			const reader = new FileReader();
			reader.onload = () => {
				appState.company.logo = reader.result;
				save();
			};
			reader.readAsDataURL(file);
		} else {
			alert('Please select a valid image (.png, .jpg, or .webp)');
		}
	}

	function save() {
		localStorage.setItem('invoiceData', JSON.stringify(appState));
	}

	function addItem() {
		if (itemDesc != '' && itemPrice > 0 && itemQty > 0) {
			appState.items = [...appState.items, { desc: itemDesc, price: itemPrice, quantity: itemQty }];

			itemDesc = '';
			itemPrice = 1;
			itemQty = 1;

			save();
			document.getElementById('descBox').focus();
		}
	}

	function deleteItem(index) {
		appState.items = appState.items.filter((_, i) => i !== index);
		save();
	}

	function reset() {
		appState = {
			company: { name: 'Piper Piper', logo: '' },
			invoice: {
				number: '1277',
				created: 'May 24, 2014',
				due: 'June 24, 2014',
				from: 'Piper Piper Inc, Apartment 3B, 555 Silicoin Street, Palo Alto, California, 94301',
				to: 'Intersite, Suite 69, Adult Entertainment, Los Angeles, California 90028'
			},
			items: [
				{ desc: 'Data Compression Service', price: '1200', quantity: '50' },
				{ desc: 'Cloud Storage (200GB)', price: '30000', quantity: '1' },
				{ desc: 'Video Encoding & Streaming Optimization', price: '10000', quantity: '7' }
			],
			tax: '18',
			note: "Due to a mishap with Russ's bottle being on delete, some files were accidentally removed.",
			payment: {
				accountNumber: '9876 5432 1098',
				accountName: 'Piper Piper Inc',
				bank: 'Silicon Valley Bank'
			},
			contact: { mail: 'jared.dunn@piedpiper.com', phone: '+1 650-555-1234' }
		};

		save();
	}

	// Startup ------------------------------------------------------------

	onMount(() => {
		const savedData = localStorage.getItem('invoiceData');
		if (savedData) {
			appState = JSON.parse(savedData);
		} else {
			reset();
		}
	});

	let itemDesc = '';
	let itemPrice = 1;
	let itemQty = 1;

	$: subTotal = appState.items.reduce((total, item) => {
		return total + item.price * item.quantity;
	}, 0);
	$: totalDue = subTotal + (appState.tax / 100) * subTotal;
</script>

<svelte:head>
	<title>Billie - No Strings Attached Invoice Generator</title>
	<meta
		name="description"
		content="Create and download invoices instantly, with zero signups or tracking."
	/>
</svelte:head>

<svelte:body on:click={() => save()} />

<div
	class="print:rotate-0 print:m-0 -rotate-1 max-w-screen-md mx-auto -mt-20 px-6 py-8 flex flex-col space-y-6 font-inter bg-white print:shadow-none shadow-lg"
>
	<!-- Company & Invoice Details --------------------------------------->

	<div class="flex flex-row justify-between">
		<div class="flex flex-col gap-4">
			<div>
				{#if appState.company.logo}
					<div class="flex flex-row items-center print:space-x-0 space-x-4">
						<div class="print:hidden flex flex-col border rounded-xl shadow-md">
							<button
								class="p-2"
								on:click={() => {
									appState.logo = null;
								}}
							>
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
						<img
							src={appState.company.logo}
							alt="Company Logo"
							class="max-h-18 max-w-40 object-cover m-0"
						/>
					</div>
				{:else}
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
			<input class="font-bold text-2xl" type="text" bind:value={appState.company.name} />
		</div>

		<div class="relative flex flex-col items-end gap-1">
			<h2 class="font-bold text-2xl text-[#1E6F5C] text-right">
				INVOICE :
				<input
					type="text"
					size="2"
					placeholder="2341"
					maxlength="4"
					bind:value={appState.invoice.number}
				/>
			</h2>
			<div class="flex flex-col space-y-2 absolute -right-20">
				<button
					on:click={() => {
						reset();
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
					bind:value={appState.invoice.created}
					type="text"
					size="10"
					placeholder="May 24,2014"
					maxlength="13"
				/>
			</p>
			<p>
				Due :
				<input
					bind:value={appState.invoice.due}
					type="text"
					size="10"
					placeholder="May 24,2014"
					maxlength="13"
				/>
			</p>
		</div>
	</div>

	<hr />

	<!-- From & To  ------------------------------------------------------>

	<div class="flex flex-row justify-between">
		<div>
			<h4 class="mb-4 font-bold">From</h4>
			<textarea
				style="resize: none;"
				placeholder="Piper Piper, Apartment 3B, 555 Silicon Street, Palo Alto, California 94301"
				cols="30"
				rows="5"
				maxlength="150"
				bind:value={appState.invoice.from}
			></textarea>
		</div>
		<div>
			<h4 class="mb-4 font-bold">To</h4>
			<textarea
				style="resize: none;"
				placeholder="Piper Piper, Apartment 3B, 555 Silicon Street, Palo Alto, California 94301"
				cols="30"
				rows="5"
				maxlength="150"
				bind:value={appState.invoice.to}
			></textarea>
		</div>
	</div>

	<!-- New Item Form --------------------------------------------------->

	<form class="print:hidden flex flex-row justif-between gap-2">
		<button
			disabled={itemDesc == ''}
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
			{itemPrice * itemQty}
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
		{#each appState.items as item, index}
			<div class="flex flex-row even:bg-gray-50 break-all">
				<button
					on:click={() => {
						deleteItem(index);
					}}
					class="absolute -ml-20 print:hidden rounded-full p-2 bg-white shadow cursor-pointer active:scale-90 transition-all duration-100 ease-in"
				>
					<Trash2 color="#C96868" />
				</button>

				<p contenteditable="true" class="p-2 border grow break-all">{item.desc}</p>
				<input class="p-2 border" maxlength="7" size="6" type="text" bind:value={item.price} />
				<input class="p-2 border" maxlength="4" size="4" type="text" bind:value={item.quantity} />
				<input
					disabled
					class="p-2 border"
					maxlength="4"
					size="12"
					type="text"
					placeholder={item.price * item.quantity}
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
			<input class="p-2 border" maxlength="2" size="12" type="text" bind:value={appState.tax} />
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

	<div>
		<h4 class="mb-4 font-bold">Note</h4>
		<textarea
			bind:value={appState.note}
			style="resize: none;"
			placeholder="Piper Piper, Apartment 3B, 555 Silicon Street, Palo Alto, California 94301"
			cols="30"
			rows="5"
			maxlength="150"
		></textarea>
	</div>
	<p>Thank you for your business</p>
	<hr />

	<div class="flex flex-row justify-between break-inside-avoid">
		<div>
			<h4 class="mb-4 font-bold">Payment Info</h4>
			<ul>
				<li class="m-1 flex flex-row gap-1">
					<p>Account</p>
					<span>:</span>
					<input
						type="text"
						maxlength="12"
						placeholder="987654321098"
						bind:value={appState.payment.accountNumber}
					/>
				</li>
				<li class="m-1 flex flex-row gap-1">
					<p>A/C Name</p>
					<span>:</span>
					<input
						type="text"
						minlength="0"
						maxlength="28"
						placeholder="Piper Piper Inc"
						bind:value={appState.payment.accountName}
					/>
				</li>
				<li class="m-1 flex flex-row gap-1">
					<p>Bank Name</p>
					<span>:</span>
					<input
						type="text"
						minlength="0"
						maxlength="28"
						placeholder="Silicion Valley Bank"
						bind:value={appState.payment.bank}
					/>
				</li>
			</ul>
		</div>

		<div>
			<h4 class="mb-4 font-bold">Contact</h4>
			<ul>
				<li class="m-1 flex flex-row gap-1">
					<p>Mail</p>
					<span>:</span>
					<input
						type="email"
						maxlength="28"
						placeholder="jared.dunn@piedpiper.com"
						bind:value={appState.contact.mail}
					/>
				</li>
				<li class="m-1 flex flex-row gap-1">
					<p>Phone</p>
					<span>:</span>
					<input
						type="phone"
						minlength="0"
						maxlength="14"
						placeholder="+91 9876543210"
						bind:value={appState.contact.phone}
					/>
				</li>
			</ul>
		</div>
	</div>
</div>
