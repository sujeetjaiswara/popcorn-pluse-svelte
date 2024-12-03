<script lang="ts">
	import { Button, CardPlaceholder, Search, Select, Spinner } from 'flowbite-svelte';
	import { onMount } from 'svelte';
	import MoveListItem from '../components/MoveListItem.svelte';
	import { PUBLIC_API_KEY, PUBLIC_ENDPOINT } from '$env/static/public';

	let selectedCategory = $state('popular');
	let page = 1;
	let movies: any[] = $state([]);
	let searchTerm = $state('');
	let isLoading = $state(false);
	let isLoadingMore = $state(false);

	const categories = [
		{ value: 'popular', name: 'Popular' },
		{ value: 'top_rated', name: 'Top Rated' },
		{ value: 'now_playing', name: 'Now Playing' },
		{ value: 'upcoming', name: 'Upcoming' }
	];

	onMount(() => {
		movies = [];
		getMovieByCategory();
	});

	async function getMovieByCategory() {
		isLoading = true;

		const apiUrl = `${PUBLIC_ENDPOINT}/movie/${selectedCategory}?api_key=${PUBLIC_API_KEY}&page=${page}`;
		return fetch(apiUrl)
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
			const apiUrl = `${PUBLIC_ENDPOINT}/search/collection?query=${searchTerm}&include_adult=false&language=en-US&api_key=${PUBLIC_API_KEY}&page=${page}`;
			return fetch(apiUrl)
				.then((response) => response.json())
				.then((data) => {
					movies = data.results;
				})
				.catch((error) => console.error('Error:', error));
		} else {
			getMovieByCategory();
		}
	}
</script>

<svelte:head>
	<title>Movies</title>
</svelte:head>

<div class="flex items-center justify-between space-x-2">
	<div class="w-full">
		<Search bind:value={searchTerm} on:keyup={searchMovie} size="md" />
	</div>
	<div class="w-2/6">
		<Select items={categories} bind:value={selectedCategory} on:change={filterMovies} />
	</div>
</div>

<div class="mt-5 flex flex-wrap">
	{#if isLoading && !isLoadingMore}
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
	{:else}
		{#each movies as movie}
			<MoveListItem {movie} />
		{/each}
	{/if}
</div>

<div class="flex justify-center my-3">
	<Button on:click={loadMore} disabled={isLoadingMore} type="button">
		{#if isLoadingMore}
			<Spinner class="me-3" size="4" color="white" />Loading ...
		{:else}
			Load More
		{/if}
	</Button>
</div>
