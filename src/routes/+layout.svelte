<script>
	import '../app.css';
	import { Navbar, NavBrand, NavLi, NavUl, NavHamburger } from 'flowbite-svelte';
</script>

<Navbar class="py-[2px]H" color="primary">
	<NavBrand href="/">
		<span class="text-2xl">🍿</span>
		<span class="self-center whitespace-nowrap text-xl font-semibold dark:text-white">
			Popcorn Pluse
		</span>
	</NavBrand>
	<!-- <NavHamburger />
	<NavUl>
		<NavLi href="/">Home</NavLi>
		<NavLi href="/about">About</NavLi>
		<NavLi href="/docs/components/navbar">Navbar</NavLi>
		<NavLi href="/pricing">Pricing</NavLi>
		<NavLi href="/contact">Contact</NavLi>
	</NavUl> -->
</Navbar>

<div class="w-10/12 mx-auto mt-3">
	<slot />
</div>
