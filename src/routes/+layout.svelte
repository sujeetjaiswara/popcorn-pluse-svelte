<script lang="ts">
	import '../app.css';

	import { DarkMode, NavBrand, Navbar } from 'flowbite-svelte';
	import { GithubSolid } from 'flowbite-svelte-icons';
	import { fly } from 'svelte/transition';

	interface Props {
		data: any;
		children?: import('svelte').Snippet;
	}

	let { data, children }: Props = $props();
</script>

<Navbar class="py-[10px] border-b" color="default">
	<NavBrand href="/">
		<img src="logo.png" alt="" class="w-8" />
		<span class="self-center whitespace-nowrap text-xl font-semibold dark:text-white">
			Popcorn Pluse
		</span>
	</NavBrand>
	<div class="flex items-center space-x-3">
		<a
			href="https://github.com/sujeetjaiswara/popcorn-pluse-svelte"
			target="_blank"
			class="flex text-sm"
		>
			<GithubSolid ariaLabel="View on GitHub" />
		</a>
		<DarkMode size="sm" />
	</div>
</Navbar>

<!-- Page container -->
{#key data.url}
	<div
		class="w-9/12 mx-auto mt-3"
		in:fly={{ x: -50, duration: 100, delay: 100 }}
		out:fly={{ x: 50, duration: 100 }}
	>
		{@render children?.()}
	</div>
{/key}
