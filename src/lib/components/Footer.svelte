<script>
	const date = new Date();
	export let repo = 'repo-name';
	const anchorStyle = 'decoration-2 underline underline-offset-4 font-semibold ';
</script>

<footer class="print:hidden text-stone-800 my-16">
	<p class="p-2 m-2 text-center">
		© {date.getFullYear()}
		<a
			target="_blank"
			class="{anchorStyle}  decoration-sky-500"
			href={'https://github.com/commitsovercoffee/' + repo}>{repo}</a
		>
		· Built by
		<a
			target="_blank"
			class="{anchorStyle} decoration-indigo-500"
			href="https://commitsovercoffee.com">commitsovercoffee</a
		>
	</p>
</footer>
