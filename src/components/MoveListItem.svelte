<script>
	import { Badge } from 'flowbite-svelte';
</script>

<a
	href="/move-details"
	class="hover:outline hover:outline-dark-500 hover:outline-2 hover:rounded m-1"
>
	<div class="flex flex-col w-[150px] rounded">
		<div>
			<img
				src="https://image.tmdb.org/t/p/w220_and_h330_face/a6syn9qcU4a54Lmi3JoIr1XvhFU.jpg"
				alt=""
				class="rounded-t-md"
			/>
		</div>
		<div class="px-2 py-1 border border-t-0 rounded-b-md pb-3">
			<h4 class="font-semibold">The Family Plan</h4>
			<Badge color="primary">8/10</Badge>
		</div>
	</div>
</a>
