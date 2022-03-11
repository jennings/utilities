<script context="module" lang="ts">
	export const prerender = true;
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<section>
	<h1>
		Stephen's Toolbox
	</h1>

	<p>
		Little utilities I need occasionally.
		Also, an excuse to learn <a href="https://svelte.dev/">Svelte</a>.
	</p>

	<p>
		I know there are a thousand of these things out there. But I just google for one every time I
		need it, and I have no idea when I've landed on one that secretly sends all my data to the
		Empire of Octavia.
	</p>
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 1;
	}

	h1 {
		width: 100%;
	}

	.welcome {
		position: relative;
		width: 100%;
		height: 0;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.welcome img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}
</style>
