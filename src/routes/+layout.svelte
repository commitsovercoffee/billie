<script>
	import '../app.css';

	// Supports weights 100-900
	import '@fontsource-variable/inter';

	const creditStyle = 'underline underline-offset-8 decoration-dashed';
</script>

<slot />
<footer class="print:hidden">
	<p id="credits" class="p-2 m-2 my-12 text-center align-middle text-stone-800">
		© 2024
		<a class={creditStyle} href="https://github.com/commitsovercoffee/bill-me">bill-me</a>
		· Built by
		<a class={creditStyle} href="https://commitsovercoffee.com">commitsovercoffee</a>
	</p>
</footer>
