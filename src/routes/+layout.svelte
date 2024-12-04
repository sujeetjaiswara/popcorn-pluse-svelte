<script lang="ts">
	import { Button } from '$lib/components/ui/button';
	import { ScrollArea } from '$lib/components/ui/scroll-area/index.js';
	import Github from 'lucide-svelte/icons/github';
	import SunMoon from 'lucide-svelte/icons/sun-moon';
	import { fly } from 'svelte/transition';
	import '../app.css';

	interface Props {
		data: any;
		children?: import('svelte').Snippet;
	}

	let { data, children }: Props = $props();
</script>

<div class="border-b py-[6px]">
	<div class="container flex justify-between">
		<a href="/" class="flex items-center text-gray-700">
			<img src="logo.png" alt="" class="h-8 w-8" />
			<span class="self-center whitespace-nowrap text-xl font-semibold dark:text-white">
				Popcorn Pluse
			</span>
		</a>
		<div class="flex items-center space-x-2">
			<Button
				variant="link"
				size="icon"
				href="https://github.com/sujeetjaiswara/popcorn-pluse-svelte"
				target="_blank"
			>
				<Github />
			</Button>
			<Button variant="ghost" size="icon">
				<SunMoon />
			</Button>
		</div>
	</div>
</div>

<!-- Page container -->
{#key data.url}
	<ScrollArea class="h-[calc(100vh-3.8rem)]">
		<div
			class="container mx-auto mt-3"
			in:fly={{ x: -50, duration: 100, delay: 100 }}
			out:fly={{ x: 50, duration: 100 }}
		>
			{@render children?.()}
		</div>
	</ScrollArea>
{/key}
