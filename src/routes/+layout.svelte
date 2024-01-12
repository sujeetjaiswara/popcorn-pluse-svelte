<script>
	import '../app.css';
	import { Navbar, NavBrand, NavLi, NavUl, NavHamburger } from 'flowbite-svelte';
	import { GithubSolid } from 'flowbite-svelte-icons';
	import { DarkMode } from 'flowbite-svelte';
</script>

<Navbar class="py-[10px] border-b" color="default">
	<NavBrand href="/">
		<span class="text-2xl">🍿</span>
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

<div class="w-9/12 mx-auto mt-3">
	<slot />
</div>
