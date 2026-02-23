<script lang="ts">
	import { Bolt } from '@lucide/svelte';
	import { X } from '@lucide/svelte';
	import * as Dialog from '$lib/components/ui/dialog/index.js';
	import { ScrollArea } from '$lib/components/ui/scroll-area/index.js';
	import { Separator } from '$lib/components/ui/separator/index.js';
	import { Input } from '$lib/components/ui/input/index.js';
	import { Button } from '$lib/components/ui/button/index.js';

	let { backgrounds = $bindable() }: { backgrounds: string[] } = $props();

	let newBackgroundSrc: string = $state('');
</script>

<Dialog.Root>
	<Dialog.Trigger class="absolute bottom-0 left-0 z-5 m-3 rounded p-1 text-white  hover:bg-white/20"
		><Bolt size="32" /></Dialog.Trigger
	>
	<Dialog.Content class="max-w-fit! min-w-[50vw]">
		<Dialog.Header>
			<Dialog.Title>Settings</Dialog.Title>
			<Dialog.Description>
				<h1 class="m-2">Background Video Sources</h1>
				<ScrollArea class="h-48 rounded border border-gray-100 shadow-sm">
					<div class="p-4">
						{#each backgrounds as background, index (index)}
							<div class="flex justify-between text-sm">
								{background}
								<button
									class="ml-3 rounded text-red-500"
									onclick={() => backgrounds.splice(index, 1)}><X /></button
								>
							</div>
							{#if index !== backgrounds.length - 1}
								<Separator class="my-2" />
							{/if}
						{/each}
					</div>
				</ScrollArea>
				<div class="mt-3 flex gap-3">
					<Input bind:value={newBackgroundSrc} placeholder="new background" />
					<Button
						onclick={() => {
							backgrounds.push(newBackgroundSrc);
							newBackgroundSrc = '';
						}}>add</Button
					>
				</div>
			</Dialog.Description>
		</Dialog.Header>
	</Dialog.Content>
</Dialog.Root>
