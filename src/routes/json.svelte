<script lang="ts">
	import clipboardCopy from 'clipboard-copy';

	let indentSize = 2;

	let json = '';

	$: formatted = format(json, Number(indentSize));

	function format(json, indentSize) {
		if (json === '') {
			return '';
		}

		let obj;
		try {
			obj = JSON.parse(json);
		} catch (err) {
			return `Invalid JSON: ${err}`;
		}

		return JSON.stringify(obj, null, indentSize);
	}

	function copy() {
		clipboardCopy(formatted);
	}
	function highlightThis(this: HTMLElement) {
		window.getSelection().selectAllChildren(this)
	}
</script>

<svelte:head>
	<title>Format JSON</title>
</svelte:head>

<div>
	<h1>Format JSON</h1>

	<blockquote class="secret">
		<p>Psst... You could also do this with: <code on:click={highlightThis}>Get-Clipboard | jq .</code></p>
	</blockquote>

	<div>
		Indent <input bind:value={indentSize} type="number" /><br />

		Input:<br />
		<textarea rows="5" cols="80" bind:value={json} /><br />

		Output:<br />
		<textarea readonly rows="5" cols="80" bind:value={formatted} /><br/>
		<button on:click={copy}>Copy</button>
	</div>
</div>

<style>
	.secret {
		font-style: italic;
	}
	.secret code {
		font-style: normal;
	}
</style>
