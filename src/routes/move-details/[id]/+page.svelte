<script lang="ts">
	import { afterNavigate } from '$app/navigation';
	import { page } from '$app/stores';
	import { PUBLIC_API_KEY, PUBLIC_ENDPOINT } from '$env/static/public';
	import { Badge } from '$lib/components/ui/badge/index.js';
	import * as Carousel from '$lib/components/ui/carousel/index.js';
	import { Skeleton } from '$lib/components/ui/skeleton/index.js';
	import { onMount } from 'svelte';
	import MoveListItem from '../../../components/MoveListItem.svelte';

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
	<div class="bg-dark text-dark flex h-[27rem] rounded-md border">
		<div class="p-3">
			<Skeleton class="max-h-[375px] min-h-[375px] w-[275px] rounded-md" />
		</div>
		<div class="flex w-full flex-col p-5">
			<Skeleton class="mb-2 h-[20px] w-[350px]" />
			<Skeleton class="mb-2 h-[10px] w-full" />
			<Skeleton class="mb-2 h-[10px] w-full" />
			<Skeleton class="mb-2 h-[10px] w-[240px]" />
			<Skeleton class="mb-2 h-[10px] w-[250px]" />
		</div>
	</div>
{:then movie}
	<div class="flex h-[27rem] rounded-md border">
		{#if movie?.poster_path}
			<div class="p-3">
				<img
					src={getPoster(movie?.poster_path)}
					alt=""
					class="min-w-[275px] max-w-[275px] rounded-md"
				/>
			</div>
		{/if}

		<div class="p-5">
			<h2 class="mb-2 flex items-center gap-2 text-2xl font-semibold">
				{movie?.title}
				{#if movie?.vote_average}
					<Badge variant="default">
						{Math.floor(movie?.vote_average)}/10
					</Badge>
				{/if}
			</h2>

			<p class="mb-2 text-base text-muted-foreground">
				{movie?.overview}
			</p>

			{#if movie?.spoken_languages}
				<div class="mb-1 flex">
					<div class="me-1 font-medium">Available In :</div>
					<div class="text-muted-foreground">
						{#each movie.spoken_languages as language, index}
							{language.name}
							{#if movie.spoken_languages.length - 1 !== index}
								<span class="me-1">,</span>
							{/if}
						{/each}
					</div>
				</div>
			{/if}

			{#if movie.genres}
				<div class="mb-1 flex">
					<div class="me-1 font-medium">Genres :</div>
					<div class="text-muted-foreground">
						{#each movie?.genres as genre, index}
							{genre.name}
							{#if movie?.genres.length - 1 !== index}
								<span class="me-1">,</span>
							{/if}
						{/each}
					</div>
				</div>
			{/if}

			{#if movie.budget}
				<div class="mb-1 flex">
					<div class="me-1 font-medium">Budget :</div>
					<div class="text-muted-foreground">
						{movie?.budget}
					</div>
				</div>
			{/if}

			{#if movie?.homepage}
				<div class="mb-1 flex">
					<div class="me-1 font-medium">Homepage :</div>
					<div class="text-muted-foreground">
						<a
							href={movie?.homepage}
							target="_blank"
							rel="noreferrer"
							class="btn-link text-primary-600 hover:text-primary"
						>
							{movie?.homepage}
						</a>
					</div>
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

<div class="mt-5">
	<h2 class="mb-1 text-lg font-semibold">Similar Movies</h2>
	{#await similarMovies}
		<div class="grid grid-cols-12 gap-2 md:grid-cols-6">
			<Skeleton class="h-[387px] rounded-md" />
			<Skeleton class="h-[387px] rounded-md" />
			<Skeleton class="h-[387px] rounded-md" />
			<Skeleton class="h-[387px] rounded-md" />
			<Skeleton class="h-[387px] rounded-md" />
			<Skeleton class="h-[387px] rounded-md" />
		</div>
	{:then similarMovies}
		<Carousel.Root>
			<Carousel.Content>
				{#each similarMovies as movie}
					<Carousel.Item class="basis-1/2 md:basis-1/6">
						{#if movie}
							<MoveListItem {movie} />
						{/if}
					</Carousel.Item>
				{/each}
			</Carousel.Content>
			<Carousel.Previous />
			<Carousel.Next />
		</Carousel.Root>
	{:catch error}
		<p style="color: red">{error.message}</p>
	{/await}
</div>
