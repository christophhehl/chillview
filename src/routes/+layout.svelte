<script lang="ts">
	import { goto } from '$app/navigation';

	let { children } = $props();
	goto('view');
</script>

{@render children()}
