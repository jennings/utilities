<script lang="ts">
	import clipboardCopy from "clipboard-copy";
	import prettier from "prettier";
	import markdownParser from "prettier/parser-markdown.js";
	import cssParser from "prettier/parser-postcss.js";

	let unformatted = "";

	type ParserOptions = { parser: "markdown" } | { parser: "css" };

	let parserOptions: ParserOptions = { parser: "markdown" };

	$: [formatted, error] = format(unformatted, parserOptions);

	function format(unformatted: string, parserOptions: ParserOptions): [string, Error] {
		try {
			return [
				prettier.format(unformatted, {
					...parserOptions,
					plugins: [markdownParser, cssParser]
				}),
				null
			];
		} catch (err) {
			return [unformatted, err];
		}
	}

	function copy() {
		clipboardCopy(formatted);
	}
</script>

<svelte:head>
	<title>Prettier</title>
</svelte:head>

<div>
	<h1>Prettier</h1>

	<div class="textareas">
		<div>
			<h2>Input</h2>
			<div>
				<label>
					Language
					<select
						value={parserOptions.parser}
						on:change={(e) => {
							parserOptions = {
								// @ts-expect-error
								parser: e.currentTarget.value
							};
						}}
					>
						<option value="markdown">Markdown</option>
						<option value="css">CSS</option>
					</select>
				</label>
			</div>
			<textarea bind:value={unformatted} /><br />
		</div>
		<div>
			<h2>Output</h2>
			<div>
				<button on:click={copy}>Copy</button>
			</div>
			<textarea readonly bind:value={formatted} /><br />
		</div>
	</div>
	<div>
		{#if error}
		<pre><code>{error}</code></pre>
		{/if}
	</div>
</div>

<style>
	.textareas {
		display: flex;
		flex-direction: row;
	}
	.textareas > * {
		flex: 1 0 auto;
	}
	textarea {
		width: 90%;
		height: 20em;
	}
</style>
