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

<div class="mt-5 flex justify-center space-x-4 space-y-2 flex-wrap">
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
	<MoveListItem />
</div>
