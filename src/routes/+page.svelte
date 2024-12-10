<script lang="ts">
	import LoaderCircle from 'lucide-svelte/icons/loader-circle';
	import Search from 'lucide-svelte/icons/search';
	import { onMount } from 'svelte';
	import MoveListItem from '../components/MoveListItem.svelte';
	import { PUBLIC_API_KEY, PUBLIC_ENDPOINT } from '$env/static/public';
	import { Button } from '$lib/components/ui/button/index.js';
	import { Input } from '$lib/components/ui/input';
	import { Select, SelectTrigger, SelectContent, SelectItem } from '$lib/components/ui/select';
	import { Skeleton } from '$lib/components/ui/skeleton/index.js';
	import * as Card from '$lib/components/ui/card/index.js';

	let selectedCategory = $state('popular');
	let movies: any[] = $state([]);
	let searchTerm = $state('');
	let isLoading = $state(false);
	let isLoadingMore = $state(false);

	let page = 1;

	const INCLUDE_ADULT = false;
	const LANG = 'en-IN'; //'hi-IN';
	const REGION = 'IN';

	const categories: any[] = $state([
		{ value: 'popular', name: 'Popular' },
		{ value: 'top_rated', name: 'Top Rated' },
		{ value: 'now_playing', name: 'Now Playing' },
		{ value: 'upcoming', name: 'Upcoming' }
	]);

	const triggerContent = $derived(
		categories.find((f) => f.value === selectedCategory)?.name ?? 'Select a category'
	);

	onMount(() => {
		movies = [];
		getMovieByCategory();
	});

	async function getMovieByCategory() {
		isLoading = true;

		let url = `${PUBLIC_ENDPOINT}/movie/${selectedCategory}?api_key=${PUBLIC_API_KEY}`;
		url += `&language=${LANG}`;
		url += `&region=${REGION}`;
		url += `&include_adult=${INCLUDE_ADULT}`;
		url += `&page=${page}`;

		return fetch(url)
			.then((response) => response.json())
			.then((data) => {
				// Create a Set to keep track of unique movie IDs
				const uniqueMovieIds = new Set(movies.map((movie) => movie.id));

				// Filter out duplicates by checking if the movie ID is already in the Set
				const uniqueMovies = data.results.filter((movie: any) => !uniqueMovieIds.has(movie.id));

				// Concatenate unique movies to the existing movies array
				movies = [...movies, ...uniqueMovies];

				isLoading = false;
			})
			.catch((error) => {
				console.error('Error:', error);
				isLoading = false;
			});
	}

	async function loadMore() {
		page = page + 1;
		isLoadingMore = true;
		await getMovieByCategory();
		isLoadingMore = false;
	}

	function filterMovies() {
		movies = [];
		getMovieByCategory();
	}

	function searchMovie() {
		page = 1;

		if (searchTerm) {
			let url = `${PUBLIC_ENDPOINT}/search/movie?query=${searchTerm}`;
			url += `&include_adult=${INCLUDE_ADULT}`;
			url += `&language=${LANG}`;
			url += `&region=${REGION}`;
			url += `&api_key=${PUBLIC_API_KEY}`;
			url += `&page=${page}`;

			return fetch(url)
				.then((response) => response.json())
				.then((data) => {
					movies = data.results;
				})
				.catch((error) => console.error('Error:', error));
		} else {
			movies = [];
			getMovieByCategory();
		}
	}
</script>

<svelte:head>
	<title>Movies</title>
</svelte:head>

<div class="flex items-center justify-between space-x-2">
	<div class="w-full">
		<form>
			<div class="relative">
				<Search
					class="absolute left-2 top-[50%] h-4 w-4 translate-y-[-50%] text-muted-foreground"
				/>
				<Input placeholder="Search" class="pl-8" bind:value={searchTerm} onkeyup={searchMovie} />
			</div>
		</form>
	</div>
	<div class="w-2/6">
		<Select type="single" bind:value={selectedCategory} onValueChange={filterMovies}>
			<SelectTrigger class="w-full">
				{triggerContent}
			</SelectTrigger>
			<SelectContent>
				{#each categories as category}
					<SelectItem value={category.value} label={category.name}>{category.name}</SelectItem>
				{/each}
			</SelectContent>
		</Select>
	</div>
</div>

<div class="mt-5 grid grid-cols-2 gap-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-8">
	{#if isLoading && !isLoadingMore}
		{#each { length: 20 }, rank}
			<Card.Root class="hover:outline-dark-500 hover:outline hover:outline-2">
				<Card.Content class="p-0">
					<Skeleton class="h-[225px] rounded-none " />
				</Card.Content>
				<Card.Footer class="flex flex-col items-start p-2">
					<Skeleton class="mb-1 h-[12px] w-[100px] rounded" />
					<Skeleton class="h-[12px] w-[50px] rounded" />
				</Card.Footer>
			</Card.Root>
		{/each}
	{:else}
		{#each movies as movie}
			<MoveListItem {movie} />
		{/each}
	{/if}
</div>

<div class="my-3 flex justify-center">
	<Button onclick={loadMore} disabled={isLoadingMore}>
		{#if isLoadingMore}
			<LoaderCircle class="animate-spin" />
			Loading...
		{:else}
			Load More
		{/if}
	</Button>
</div>
