<script lang="ts">
	import { page } from '$app/stores';
	import { Badge, CardPlaceholder, ImagePlaceholder } from 'flowbite-svelte';
	import { onMount } from 'svelte';
	import MoveListItem from '../../../components/MoveListItem.svelte';
	import { afterNavigate } from '$app/navigation';
	import { PUBLIC_API_KEY, PUBLIC_ENDPOINT } from '$env/static/public';

	let movie: any = {};
	let similarMovies: any = [];

	afterNavigate(() => {
		getData();
	});

	onMount(() => {
		getData();
	});

	function getData() {
		movie = getMovieDetail(Number($page.params.id));
		similarMovies = getSimilarMovies(Number($page.params.id));
	}

	async function getMovieDetail(id: number) {
		const res = await fetch(`${PUBLIC_ENDPOINT}/movie/${id}?api_key=${PUBLIC_API_KEY}`);
		const data = await res.json();
		if (res.ok) {
			return data;
		} else {
			throw new Error(data);
		}
	}

	async function getSimilarMovies(id: number) {
		const res = await fetch(`${PUBLIC_ENDPOINT}/movie/${id}/similar?api_key=${PUBLIC_API_KEY}`);
		const data = await res.json();
		if (res.ok) {
			return data.results;
		} else {
			throw new Error(data);
		}
	}

	function getPoster(movie: any) {
		return `https://image.tmdb.org/t/p/w220_and_h330_face${movie.poster_path}`;
	}
</script>

<svelte:head>
	{#await movie}
		<title>Loading...</title>
	{:then movie}
		<title>{movie?.title}</title>
	{/await}
</svelte:head>

{#await movie}
	<div class="rounded-md bg-gradient-to-r from-orange-50 to-orange-100 text-white h-[30rem] p-3">
		<ImagePlaceholder imgHeight={'60'} class="mt-8" />
	</div>
{:then movie}
	<!-- style="background: url('https://image.tmdb.org/t/p/original{movie?.poster_path}')" -->
	<div
		class="flex rounded-md bg-dark bg-cover bg-center bg-gradient-to-r from-orange-50 to-orange-100 text-dark h-[30rem]"
	>
		<div class="p-3">
			<img src={getPoster(movie)} alt="" class="rounded-md w-96" />
		</div>

		<div class="p-5">
			<h2 class="font-semibold text-3xl mb-2">
				{movie?.title}
				<Badge color="primary">
					{Math.floor(movie?.vote_average)}/10
				</Badge>
			</h2>

			<p class="mb-2">
				{movie?.overview}
			</p>

			{#if movie?.spoken_languages}
				<div class="flex mt-2">
					<div class="me-1">Available In :</div>
					<div>
						{#each movie.spoken_languages as language}
							{language.name}<span class="me-1">,</span>
						{/each}
					</div>
				</div>
			{/if}

			{#if movie.genres}
				<div class="flex mt-2">
					<div class="me-1">Genres :</div>
					<div>
						{#each movie?.genres as genre}
							{genre.name}<span class="me-1">,</span>
						{/each}
					</div>
				</div>
			{/if}

			<div class="d-flex mt-2">
				<div class="me-1">Budget :</div>
				{#if movie.budget && movie.budget > 0}
					{movie?.budget}
				{:else}
					N/A
				{/if}
			</div>

			<div class="d-flex mt-2">
				<div class="me-1">Homepage :</div>
				<div class="me-1 font-monospace">
					<a
						href={movie?.homepage}
						target="_blank"
						rel="noreferrer"
						class="btn-link text-primary-600"
					>
						{movie?.homepage}
					</a>
				</div>
			</div>
		</div>
	</div>
{:catch error}
	<!-- promise was rejected -->
	<p>Something went wrong: {error.message}</p>
{/await}

<div class="mt-2 flex flex-wrap">
	{#await similarMovies}
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
		<CardPlaceholder divClass="w-44 me-2 mb-2" />
	{:then similarMovies}
		{#each similarMovies as movie}
			{#if movie}
				<MoveListItem {movie} />
			{/if}
		{/each}
	{/await}
</div>
