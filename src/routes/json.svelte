<script lang="ts">
	import clipboardCopy from "clipboard-copy";

	let shouldIndent = true;
	let indentSize = "2";

	let json = "";

	$: formatted = format(json, shouldIndent ? Number(indentSize) : 0);

	function format(json, indentSize) {
		if (json === "") {
			return "";
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
		window.getSelection().selectAllChildren(this);
	}
</script>

<svelte:head>
	<title>Format JSON</title>
</svelte:head>

<div>
	<h1>Format JSON</h1>

	<blockquote class="secret">
		<p>Psst... You could also do this with:</p>
		<ul>
			<li>Windows: <code on:click={highlightThis}>Get-Clipboard | jq .</code></li>
			<li>macOS: <code on:click={highlightThis}>pbpaste | jq .</code></li>
		</ul>
	</blockquote>

	<div>
		<label>
			<input type="radio" name="shouldIndent" bind:group={shouldIndent} value={true} />
			<input
				bind:value={indentSize}
				type="number"
				on:click={(e) => (shouldIndent = true)}
				on:change={(e) => (shouldIndent = true)}
			/>
		</label>
		<label>
			<input type="radio" name="shouldIndent" bind:group={shouldIndent} value={false} />
			Compact
		</label>
		<br />

		Input:<br />
		<textarea rows="5" cols="80" bind:value={json} /><br />

		Output:<br />
		<textarea readonly rows="5" cols="80" bind:value={formatted} /><br />
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
