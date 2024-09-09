<script>
	export let heading = 'Questions?';
	export let data = [
		{
			label: 'email', // if empty, label isnt rendered
			value: '',
			placeholder: 'jared.dunn@piedpiper.com',
			type: 'email', // text, tel, email
			pattern: '.+@example.com',
			minlength: 10,
			maxlength: 20
		}
	];
</script>

<div>
	<h4 class="mb-4 font-bold">{heading}</h4>
	<ul>
		{#each data as detail}
			<li class="m-1 flex flex-row gap-1">
				{#if detail.label != ''}
					<p>{detail.label}</p>
					<span>:</span>
				{/if}
				<input
					type={detail.type}
					pattern={detail.pattern}
					maxlength={detail.maxlength}
					placeholder={detail.placeholder}
					bind:innerText={detail.value}
					contenteditable="true"
				/>
			</li>
		{/each}
	</ul>
</div>
