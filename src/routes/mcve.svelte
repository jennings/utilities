<script type="ts">
	import clipboardCopy from "clipboard-copy";
	import { marked } from "marked";

	type Problem = null | "screenshot" | "snippet";

	let problem: Problem = null;
	let react = false;

	$: message = buildMessage(problem, react);

	function buildMessage(problem: Problem, react: boolean) {
		let paragraphs = [
			`Can you post a [minimal, reproducible example](https://stackoverflow.com/help/minimal-reproducible-example) so we can see the problem?`,
			`Having an example we can run ourselves really helps find the issue quickly. Also, by trying to prepare an isolated example, you'll often discover the problem yourself.`,
			problem === "screenshot" &&
				`Screenshots are hard to read and often don't contain the code causing the problem. Providing the code as text lets others copy it into their own editor to work with.`,
			problem == "snippet" &&
				`It looks like you provided just a snippet, but the problem might be elsewhere. If you provide an example that can be run, we won't have to guess where the problem _might_ be.`,
			react &&
				`Since this is React, if you can create a new, empty project and build the example in App.jsx (or App.tsx), that is enough. We probably don't need package.json and all the other files in a React project.`
		];

		return paragraphs.filter(Boolean).join("\n\n");
	}
</script>

<svelte:head>
	<title>MCVE generator</title>
</svelte:head>

<h1>Minimal, complete, verifiable example reply template</h1>
<p>
	Generates a reply asking for a
	<a href="https://stackoverflow.com/help/minimal-reproducible-example"
		>minimal, complete, verifiable example
	</a>.
</p>

<h2>Options</h2>

<h3>Problems</h3>
<div>
	<label>
		<input type="radio" name="problem" bind:group={problem} value={null} />
		None
	</label><br />
	<label>
		<input type="radio" name="problem" bind:group={problem} value="screenshot" />
		Screenshot of code
	</label><br />
	<label>
		<input type="radio" name="problem" bind:group={problem} value="snippet" />
		Only provided snippets
	</label>
</div>
<div>
	<label>
		<input type="checkbox" bind:checked={react} /> Mention React
	</label>
</div>

<div class="output">
	<div>
		<h2>Markdown</h2>
		<button on:click={() => clipboardCopy(message)}>Copy to clipboard</button>
		<textarea class="markdown" bind:value={message} />
	</div>
	<div>
		<h2>Rendered</h2>
		<div class="preview">
			{@html marked(message)}
		</div>
	</div>
</div>

<style>
	.output {
		display: flex;
		flex-direction: row;
	}
	.output > * {
		flex: 1 0;
		padding: 1em;
		display: flex;
		flex-direction: column;
	}
	.markdown {
		width: 100%;
		flex-grow: 1;
	}
	.preview {
		border: solid 1px black;
		padding: 0.75em;
		width: 100%;
	}
</style>
