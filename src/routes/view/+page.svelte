<script lang="ts">
	import Settings from '$lib/components/Settings.svelte';
	import Song from '$lib/components/Song.svelte';
	import { Input } from '$lib/components/ui/input/index.js';
	import { Button } from '$lib/components/ui/button/index.js';
	import { Check } from '@lucide/svelte';
	import { SpotifyApi } from '@spotify/web-api-ts-sdk';

	let clientIdInput = $state('');

	let apiLoaded = $state(false);
	let spotifyClientId = localStorage.getItem('spotify_client_id');
	if (spotifyClientId !== null) {
		start_spotify_sync(spotifyClientId);
	}

	function start_spotify_sync(spotifyClientId: string) {
		let redirectUri =
			window.location.hostname === '127.0.0.1'
				? 'http://127.0.0.1:1420/view'
				: 'https://christophhehl.github.io/chillview/view';
		const sdk = SpotifyApi.withUserAuthorization(spotifyClientId, redirectUri, [
			'user-read-playback-state'
		]);

		// Its important to run this once before starting the interval to have enough time to finish the authorization.
		updateSong();

		apiLoaded = true;

		async function updateSong() {
			let song = await sdk.player.getCurrentlyPlayingTrack();
			songInfo.is_playing = song.is_playing;
			songInfo.name = song.item.name;
			songInfo.artists = song.item.artists.map((artist) => artist.name).join(', ');
			songInfo.image = song.item.album.images[0].url;
		}

		setInterval(updateSong, 1000);
	}

	function setClientId() {
		if (clientIdInput !== '') {
			localStorage.setItem('spotify_client_id', clientIdInput);
			start_spotify_sync(clientIdInput);
		} else {
			alert('ClientID cannot be empty!');
		}
	}

	let songInfo = $state({
		is_playing: false,
		name: '',
		artists: '',
		image: ''
	});

	let backgrounds: string[] = $state([]);
	let index = $state(0);

	let background_storage = localStorage.getItem('background_sources');
	if (background_storage !== null) {
		let background_sources = JSON.parse(background_storage);
		backgrounds = background_sources;
	}

	function handleKeydown(event) {
		if (event.key === 'ArrowRight') {
			index = (index + 1) % backgrounds.length;
		}

		if (event.key === 'ArrowLeft') {
			index = (index - 1 + backgrounds.length) % backgrounds.length;
		}
	}
</script>

<svelte:window on:keydown={handleKeydown} />

<Settings bind:backgrounds></Settings>

{#if apiLoaded}
	<Song
		is_playing={songInfo.is_playing}
		name={songInfo.name}
		artists={songInfo.artists}
		image={songInfo.image}
	/>

	{#if backgrounds.length !== 0}
		{#key index}
			<video
				autoplay
				muted
				loop
				playsinline
				class="fixed top-0 left-0 -z-10 h-screen w-screen object-cover"
			>
				<source src={backgrounds[index]} type="video/mp4" />
				Your browser does not support the video tag.
			</video>
		{/key}
	{:else}
		<div
			class="flex h-screen w-screen items-center justify-center bg-black text-3xl font-bold text-white"
		>
			no video background sources added
		</div>
	{/if}
{:else}
	<div
		class="flex h-screen w-screen flex-col items-center justify-center gap-6 bg-black text-3xl font-bold text-white"
	>
		<div>no client id provided</div>
		<div class="flex gap-3">
			<Input
				bind:value={clientIdInput}
				placeholder="client id"
				class="w-[15vw] text-center text-base text-black"
			/>
			<Button onclick={setClientId} class=" bg-green-500 text-base text-white hover:bg-green-700"
				><Check /></Button
			>
		</div>
	</div>
{/if}
