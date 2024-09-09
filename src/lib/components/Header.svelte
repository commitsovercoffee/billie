<script>
	import { LogIn } from 'lucide-svelte';
	import { UserRoundSearch } from 'lucide-svelte';
	import { Code } from 'lucide-svelte';

	import FeatureItem from '$lib/components/FeatureItem.svelte';

	export let title = 'Enter Title Here';
	export let subtitle = 'Enter subtitle here';
</script>

<div class="print:hidden bg-[#d5e8e4] pt-20 pb-40">
	<div class="max-w-screen-md mx-auto flex flex-col items-center gap-4">
		<h1 class="text-center font-bold text-4xl text-[#16423C]">
			{title}
		</h1>
		<p class="text-center opacity-80">
			{subtitle}
		</p>
		<div class="flex flex-row">
			<FeatureItem label="No Signup">
				<LogIn />
			</FeatureItem>
			<FeatureItem label="No Telemetry">
				<UserRoundSearch />
			</FeatureItem>

			<FeatureItem label="Open Source">
				<Code />
			</FeatureItem>
		</div>
	</div>
</div>
