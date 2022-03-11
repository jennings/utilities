<script lang="ts">
	import clipboardCopy from 'clipboard-copy';

	let raw = '';
	let encoded = '';

	function encode(t) {
		try {
			return btoa(t);
		} catch (err) {
			return `Error: ${err}`;
		}
	}

	function decode(t) {
		try {
			return atob(t);
		} catch (err) {
			return `${err}`;
		}
	}

	function copy(t: string) {
		clipboardCopy(t);
	}
</script>

<svelte:head>
	<title>Format JSON</title>
</svelte:head>

<div>
	<h1>Base 64</h1>

	<div>
		Text:<br />
		<textarea
			rows="5"
			cols="80"
			value={raw}
			on:input={(e) => {
				console.log('input', 'raw');
				encoded = encode(e.currentTarget.value);
			}}
		/><br />
		<button on:click={() => copy(raw)}>Copy</button><br /><br />

		Encoded:<br />
		<textarea
			rows="5"
			cols="80"
			bind:value={encoded}
			on:input={(e) => {
				console.log('input', 'encoded');
				raw = decode(e.currentTarget.value);
			}}
		/><br />
		<button on:click={() => copy(encoded)}>Copy</button>
	</div>

	<p>
		I know there are a thousand of these things out there. But I just google for one every time I
		need it, and I have no idea when I've landed on one that secretly sends all my data to the
		Empire of Octavia.
	</p>
</div>

<style>
	.secret {
		font-style: italic;
	}
	.secret code {
		font-style: normal;
	}
</style>
