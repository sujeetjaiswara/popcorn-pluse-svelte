<script>
	import { Search, Select } from 'flowbite-svelte';
	import MoveListItem from '../components/MoveListItem.svelte';

	let value = '';

	let selected = '';
	let countries = [
		{ value: 'us', name: 'Now Playing' },
		{ value: 'ca', name: 'Canada' },
		{ value: 'fr', name: 'France' }
	];
</script>

<div class="flex items-center justify-between space-x-2">
	<div class="w-full">
		<Search bind:value size="md" />
	</div>
	<div class="w-2/6">
		<Select items={countries} bind:value={selected} />
	</div>
</div>

<!-- {value} -->

<div class="flex items-center justify-between space-x-2 mt-3">
	<div class="border border-red-600 w-6/12 h-svh">
		<MoveListItem></MoveListItem>
		<MoveListItem></MoveListItem>
		<MoveListItem></MoveListItem>
		<MoveListItem></MoveListItem>
	</div>

	<div class="border border-red-600 w-full h-svh p-5">Preview</div>
</div>
