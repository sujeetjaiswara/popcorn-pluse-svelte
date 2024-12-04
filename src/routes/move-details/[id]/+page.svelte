<script lang="ts">
	import { page } from '$app/stores';
	import { onMount } from 'svelte';
	import MoveListItem from '../../../components/MoveListItem.svelte';
	import { afterNavigate } from '$app/navigation';
	import { PUBLIC_API_KEY, PUBLIC_ENDPOINT } from '$env/static/public';
	import { Badge } from '$lib/components/ui/badge/index.js';

	let movie: any = $state({});
	let similarMovies: any = $state([]);
	let watchProviders: any = $state([]);

	let isFirstLoad = true;

	afterNavigate(() => {
		if (!isFirstLoad) {
			getData();
		}

		isFirstLoad = false;
	});

	onMount(() => {
		getData();
	});

	function getData() {
		movie = getMovieDetail(Number($page.params.id));
		similarMovies = getSimilarMovies(Number($page.params.id));
		watchProviders = getWatchProviders(Number($page.params.id));
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

	async function getWatchProviders(id: number) {
		const res = await fetch(
			`${PUBLIC_ENDPOINT}/movie/${id}/watch/providers?api_key=${PUBLIC_API_KEY}`
		);
		const data = await res.json();
		if (res.ok) {
			return data.results ? data.results?.IN : '';
		} else {
			throw new Error(data);
		}
	}

	function getPoster(posterPath: any) {
		return `https://image.tmdb.org/t/p/w220_and_h330_face${posterPath}`;
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
	<div class="h-[30rem] rounded-md bg-gradient-to-r from-orange-50 to-orange-100 p-3 text-white">
		<!-- <ImagePlaceholder imgHeight={'60'} class="mt-8" /> -->
	</div>
{:then movie}
	<div
		class="bg-dark text-dark flex h-[30rem] rounded-md bg-gradient-to-r from-orange-50 to-orange-100 bg-cover bg-center"
	>
		{#if movie?.poster_path}
			<div class="p-3">
				<img
					src={getPoster(movie?.poster_path)}
					alt=""
					class="min-w-[150px] max-w-[150px] rounded-md"
				/>
			</div>
		{/if}

		<div class="p-5">
			<h2 class="mb-2 flex items-center gap-2 text-2xl font-semibold">
				{movie?.title}
				<Badge variant="default">
					{Math.floor(movie?.vote_average)}/10
				</Badge>
			</h2>

			<p class="mb-2 text-base text-muted-foreground">
				{movie?.overview}
			</p>

			{#if movie?.spoken_languages}
				<div class="mb-1 flex">
					<div class="me-1 font-medium">Available In :</div>
					{#each movie.spoken_languages as language, index}
						{language.name}
						{#if movie.spoken_languages.length - 1 !== index}
							<span class="me-1">,</span>
						{/if}
					{/each}
				</div>
			{/if}

			{#if movie.genres}
				<div class="mb-1 flex">
					<div class="me-1 font-medium">Genres :</div>
					{#each movie?.genres as genre, index}
						{genre.name}
						{#if movie?.genres.length - 1 !== index}
							<span class="me-1">,</span>
						{/if}
					{/each}
				</div>
			{/if}

			{#if movie.budget}
				<div class="mb-1 flex">
					<div class="me-1 font-medium">Budget :</div>
					{movie?.budget}
				</div>
			{/if}

			{#if movie?.homepage}
				<div class="mb-1 flex">
					<div class="me-1 font-medium">Homepage :</div>
					<a
						href={movie?.homepage}
						target="_blank"
						rel="noreferrer"
						class="btn-link text-primary-600 hover:text-primary"
					>
						{movie?.homepage}
					</a>
				</div>
			{/if}

			{#await watchProviders}
				Loading...
			{:then watchProviders}
				<div class="mt-3">
					{#if watchProviders?.buy}
						<div class="mb-2 flex space-x-1">
							<span class="me-1">Buy:</span>
							{#each watchProviders?.buy as buy}
								{#if buy.provider_name === 'Amazon Video'}
									<img
										src="https://images.justwatch.com/icon/52449861/s100/amazonprimevideo.webp"
										alt=""
										class="w-8 rounded-md"
									/>
								{:else if buy.provider_name === 'Google Play Movies'}
									<img
										src="https://images.justwatch.com/icon/169478387/s100/play.webp"
										alt=""
										class="w-8 rounded-md"
									/>
								{:else if buy.provider_name === 'Apple TV'}
									<img
										src="https://images.justwatch.com/icon/190848813/s100/itunes.webp"
										alt=""
										class="w-8 rounded-md"
									/>
								{:else if buy.provider_name === 'Sky Store'}
									<img
										src="https://images.justwatch.com/icon/208571850/s100/skystore.webp"
										alt="Sky Store"
										class="w-8 rounded-md"
									/>
								{:else if buy.provider_name === 'Rakuten TV'}
									<img
										src="https://images.justwatch.com/icon/128599720/s100/wuaki.webp"
										alt="Rakuten TV"
										class="w-8 rounded-md"
									/>
								{:else if buy.provider_name === 'Microsoft Store'}
									<img
										src="https://images.justwatch.com/icon/820542/s100/microsoft.webp"
										alt="Rakuten TV"
										class="w-8 rounded-md"
									/>
								{:else if buy.provider_name === 'YouTube'}
									<img
										src="https://images.justwatch.com/icon/59562423/s100/youtube.webp"
										alt=""
										class="w-8 rounded-md"
									/>
								{/if}
							{/each}
						</div>
					{/if}

					{#if watchProviders?.rent}
						<div class="flex items-center space-x-1">
							<span class="me-1">Rent:</span>
							{#each watchProviders?.rent as rent}
								{#if rent.provider_name === 'Amazon Video'}
									<img
										src="https://images.justwatch.com/icon/52449861/s100/amazonprimevideo.webp"
										alt=""
										class="w-8 rounded-md"
									/>
								{:else if rent.provider_name === 'Google Play Movies'}
									<img
										src="https://images.justwatch.com/icon/169478387/s100/play.webp"
										alt=""
										class="w-8 rounded-md"
									/>
								{:else if rent.provider_name === 'Apple TV'}
									<img
										src="https://images.justwatch.com/icon/190848813/s100/itunes.webp"
										alt=""
										class="w-8 rounded-md"
									/>
								{:else if rent.provider_name === 'Sky Store'}
									<img
										src="https://images.justwatch.com/icon/208571850/s100/skystore.webp"
										alt="Sky Store"
										class="w-8 rounded-md"
									/>
								{:else if rent.provider_name === 'Rakuten TV'}
									<img
										src="https://images.justwatch.com/icon/128599720/s100/wuaki.webp"
										alt="Rakuten TV"
										class="w-8 rounded-md"
									/>
								{:else if rent.provider_name === 'Microsoft Store'}
									<img
										src="https://images.justwatch.com/icon/820542/s100/microsoft.webp"
										alt="Rakuten TV"
										class="w-8 rounded-md"
									/>
								{:else if rent.provider_name === 'YouTube'}
									<img
										src="https://images.justwatch.com/icon/59562423/s100/youtube.webp"
										alt=""
										class="w-8 rounded-md"
									/>
								{/if}
							{/each}
						</div>
					{/if}
				</div>
			{:catch error}
				<p style="color: red">{error.message}</p>
			{/await}
		</div>
	</div>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}

<div class="mt-10">
	<h2 class="text-lg font-semibold">Similar Movies</h2>
	<div class="grid grid-cols-6 gap-2">
		{#await similarMovies}
			<!-- <CardPlaceholder divClass="w-44 me-2 mb-2" />
			<CardPlaceholder divClass="w-44 me-2 mb-2" />
			<CardPlaceholder divClass="w-44 me-2 mb-2" />
			<CardPlaceholder divClass="w-44 me-2 mb-2" />
			<CardPlaceholder divClass="w-44 me-2 mb-2" />
			<CardPlaceholder divClass="w-44 me-2 mb-2" />
			<CardPlaceholder divClass="w-44 me-2 mb-2" />
			<CardPlaceholder divClass="w-44 me-2 mb-2" />
			<CardPlaceholder divClass="w-44 me-2 mb-2" /> -->
		{:then similarMovies}
			{#each similarMovies as movie}
				{#if movie}
					<MoveListItem {movie} />
				{/if}
			{/each}
		{:catch error}
			<p style="color: red">{error.message}</p>
		{/await}
	</div>
</div>
