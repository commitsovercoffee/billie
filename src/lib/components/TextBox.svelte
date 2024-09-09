<script>
	export let heading = '[heading]';
	export let value = '';
</script>

<div>
	<h4 class="mb-4 font-bold">{heading}</h4>
	<textarea
		bind:value
		style="resize: none;"
		placeholder="Piper Piper, Apartment 3B, 555 Silicon Street, Palo Alto, California 94301"
		cols="30"
		rows="5"
		maxlength="150"
	></textarea>
</div>
