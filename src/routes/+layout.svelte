<script lang="ts">
	import { Button } from '$lib/components/ui/button';
	import { ScrollArea } from '$lib/components/ui/scroll-area/index.js';
	import Github from 'lucide-svelte/icons/github';
	import Moon from 'lucide-svelte/icons/moon';
	import Sun from 'lucide-svelte/icons/sun';
	import { ModeWatcher, toggleMode } from 'mode-watcher';
	import { fly } from 'svelte/transition';
	import '../app.css';

	interface Props {
		data: any;
		children?: import('svelte').Snippet;
	}

	let { data, children }: Props = $props();

	let error = $state(null);
	let reset = $state(() => {});

	function onerror(e: any, r: any) {
		error = e;
		reset = r;
	}
</script>

<svelte:boundary {onerror}>
	<ModeWatcher />

	<div class="border-b py-[6px]">
		<div class="container flex justify-between">
			<a href="/" class="flex items-center text-gray-700">
				<img src="/logo.png" alt="" class="h-8 w-8" />
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
					<Github class="h-[1.2rem] w-[1.2rem]" />
				</Button>
				<Button variant="ghost" size="icon" onclick={toggleMode}>
					<Sun
						class="h-[1.2rem] w-[1.2rem] rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0"
					/>
					<Moon
						class="absolute h-[1.2rem] w-[1.2rem] rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100"
					/>
					<span class="sr-only">Toggle theme</span>
				</Button>
			</div>
		</div>
	</div>

	<!-- Page container -->
	{#key data.url}
		<!-- <ScrollArea class="h-[calc(100vh-3.8rem)]"> -->
		<div class="custom-scrollbar h-screen overflow-y-scroll">
			<div
				class="container mx-auto mt-3"
				in:fly={{ x: -50, duration: 100, delay: 100 }}
				out:fly={{ x: 50, duration: 100 }}
			>
				{@render children?.()}
			</div>
		</div>
		<!-- </ScrollArea> -->
	{/key}
</svelte:boundary>

{#if error}
	<div class="container mx-auto mt-3 flex flex-col items-center justify-center gap-y-3">
		<pre class="text-base text-red-800">{error}</pre>
		<Button
			variant="destructive"
			onclick={() => {
				error = null;
				reset();
			}}
		>
			oops! try again
		</Button>
	</div>
{/if}
