<script lang="ts">
	import { Badge } from 'flowbite-svelte';

	interface Props {
		movie: any;
	}

	let { movie }: Props = $props();

	function getPoster(posterPath: any) {
		return `https://image.tmdb.org/t/p/w220_and_h330_face${posterPath}`;
	}
</script>

<a
	href="/move-details/{movie?.id}"
	class="hover:outline hover:outline-dark-500 hover:outline-2 hover:rounded m-1"
>
	<div class="flex flex-col w-[150px] rounded">
		{#if movie?.poster_path}
			<div>
				<img src={getPoster(movie?.poster_path)} alt="" class="rounded-t-md bg-black" />
			</div>
		{:else}
			<div class="h-[225px] bg-black"></div>
		{/if}

		<div class="px-2 py-1 border border-t-0 rounded-b-md dark:border-gray-700 pb-3">
			<h4 class="font-semibold text-sm truncate dark:text-white">
				{movie?.title}
			</h4>
			<Badge color="primary">
				{Math.floor(movie?.vote_average)}/10
			</Badge>
		</div>
	</div>
</a>
