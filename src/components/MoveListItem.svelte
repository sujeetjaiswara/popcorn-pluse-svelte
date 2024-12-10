<script lang="ts">
	import { Badge } from '$lib/components/ui/badge/index.js';
	import * as Card from '$lib/components/ui/card/index.js';
	// import { AspectRatio } from '$lib/components/ui/aspect-ratio/index.js';

	interface Props {
		movie: any;
	}

	let { movie }: Props = $props();

	function getPoster(posterPath: string): string {
		return `https://image.tmdb.org/t/p/w220_and_h330_face${posterPath}?w=220`;
	}
</script>

<a href="/move-details/{movie?.id}">
	<Card.Root class="hover:outline-dark-500 hover:outline hover:outline-2">
		<Card.Content class="p-0">
			{#if movie?.poster_path}
				{@const posterPath = getPoster(movie?.poster_path)}
				<!-- {@const posterPath = 'https://images.unsplash.com/photo-1467811884194-ae868cd3f090?q=80&w=2070&auto=format&fit=crop'} -->
				<img src={posterPath} alt="" class="rounded-t-md bg-black" />
				<!-- <enhanced:img src={posterPath} alt="An alt text" class="rounded-t-md bg-black" /> -->
			{:else}
				<div class="h-[311px] bg-black"></div>
			{/if}
		</Card.Content>
		<Card.Footer class="flex flex-col items-start p-2">
			<Card.Title class="w-full truncate text-sm font-semibold dark:text-white"
				>{movie?.title}</Card.Title
			>
			<span class="mb-1 text-xs text-slate-600">
				{movie?.release_date}
			</span>
			<Badge variant="default" class="text-[0.7rem]">
				{Math.floor(movie?.vote_average)}/10
			</Badge>
		</Card.Footer>
	</Card.Root>
</a>
