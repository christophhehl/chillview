<script lang="ts">
	let { is_playing, name, artists, image } = $props();
</script>

{#if is_playing}
	<div class="absolute top-0 left-0 flex gap-6 p-6">
		<div class="rounded-sm bg-white p-1">
			<img src={image} alt="Song Cover" class="h-[12vh] w-[12vh] rounded-sm" />
		</div>
		<div class="flex flex-col">
			<div class="mt-2 text-2xl font-bold text-white">{name}</div>
			<div class="mt-2 text-white">{artists}</div>
		</div>
	</div>
{:else}
	<div class="absolute top-0 left-0 flex gap-6 p-6">
		<div class="rounded-sm bg-white p-1">
			<div class="rounded-sm bg-gray-600">
				<div
					class="h-[12vh] w-[12vh] bg-gray-400 p-2"
					style="mask: url('/disc.svg') no-repeat center / contain; -webkit-mask: url('/disc.svg') no-repeat center / contain; mask-origin: content-box; -webkit-mask-origin: content-box;"
				></div>
			</div>
		</div>
		<div class="flex flex-col">
			<div class="mt-2 text-2xl font-bold text-white">No Song Playing</div>
		</div>
	</div>
{/if}
