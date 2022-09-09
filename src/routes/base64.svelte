<script lang="ts">
	import clipboardCopy from "clipboard-copy";

	const STD_CHAR_63 = "+";
	const STD_CHAR_64 = "/";
	type Alphabet =
		| { type: "base64" }
		| { type: "base64safe" }
		| { type: "custom"; char63: string; char64: string };

	let alphabet: Alphabet = { type: "base64" };
	let tryFormatDecodedAsJson = true;

	$: [char63, char64] = getChars(alphabet);

	type UserInput = { type: "text"; value: string } | { type: "encoded"; value: string };

	let input: UserInput = { type: "text", value: "" };

	$: text =
		input.type === "text"
			? input.value
			: decode(input.value, char63, char64, tryFormatDecodedAsJson);
	$: encoded = input.type === "encoded" ? input.value : encode(input.value, char63, char64);

	function getChars(alphabet: Alphabet): [string, string] {
		const char63 =
			alphabet.type === "base64"
				? STD_CHAR_63
				: alphabet.type === "base64safe"
				? "-"
				: alphabet.char63;
		const char64 =
			alphabet.type === "base64"
				? STD_CHAR_64
				: alphabet.type === "base64safe"
				? "_"
				: alphabet.char64;
		return [char63, char64];
	}

	function encode(t: string, char63: string, char64: string) {
		try {
			return btoa(t).replace(STD_CHAR_63, char63).replace(STD_CHAR_64, char64);
		} catch (err) {
			return `Error: ${err}`;
		}
	}

	function decode(t: string, char63: string, char64: string, tryFormatDecodedAsJson: boolean) {
		try {
			const base64 = t.replace(char63, STD_CHAR_63).replace(char64, STD_CHAR_64);
			const decoded = atob(base64);
			if (!tryFormatDecodedAsJson) {
				return decoded;
			}
			try {
				return JSON.stringify(JSON.parse(decoded), null, 2);
			} catch (err) {
				return decoded;
			}
		} catch (err) {
			return `${err}`;
		}
	}

	function copy(t: string) {
		clipboardCopy(t);
	}

	function highlightThis(this: HTMLElement) {
		window.getSelection().selectAllChildren(this);
	}
</script>

<svelte:head>
	<title>Format JSON</title>
</svelte:head>

<div>
	<div>
		<h1>Base 64</h1>

		<blockquote>
			<p class="secret">Psst... You could also do this with:</p>
			<ul>
				<li>
					Windows:
					<ul>
						<li>
							Encode: <code on:click={highlightThis}>
								[convert]::ToBase64String([system.text.encoding]::UTF8.GetBytes((get-clipboard)))
							</code>
						</li>
						<li>
							Decode: <code on:click={highlightThis}>
								[system.text.encoding]::UTF8.GetString([convert]::FromBase64String((Get-Clipboard)))
							</code>
						</li>
					</ul>
				</li>
				<li>
					macOS:
					<ul>
						<li>
							Encode: <code on:click={highlightThis}>pbpaste | base64</code>
						</li>
						<li>
							Decode: <code on:click={highlightThis}>pbpaste | base64 -D</code>
						</li>
					</ul>
				</li>
			</ul>
		</blockquote>

		<div class="options">
			<div>
				<h2>Alphabet</h2>
				<label>
					<input
						type="radio"
						name="alphabet"
						checked={alphabet.type === "base64"}
						on:change={(e) => {
							if (e.currentTarget.checked) {
								alphabet = { type: "base64" };
							}
						}}
					/>
					Base 64
				</label>
				<br />
				<label>
					<input
						type="radio"
						name="alphabet"
						checked={alphabet.type === "base64safe"}
						on:change={(e) => {
							if (e.currentTarget.checked) {
								alphabet = { type: "base64safe" };
							}
						}}
					/>
					File and URL safe
				</label>
				<br />
				<input
					type="radio"
					name="alphabet"
					checked={alphabet.type === "custom"}
					on:change={(e) => {
						if (e.currentTarget.checked) {
							alphabet = { type: "custom", char63, char64 };
						}
					}}
				/>
				<input
					style="width: 2em"
					title="Char 63"
					maxlength="1"
					value={char63}
					on:input={(e) => {
						alphabet = { type: "custom", char64, char63: e.currentTarget.value };
					}}
				/>
				<input
					style="width: 2em"
					title="Char 64"
					maxlength="1"
					value={char64}
					on:input={(e) => {
						alphabet = { type: "custom", char63, char64: e.currentTarget.value };
					}}
				/>
				<br />
			</div>
			<div>
				<h2>Options</h2>
				<label>
					<input type="checkbox" bind:checked={tryFormatDecodedAsJson} /> Format decoded text as JSON
				</label>
			</div>
		</div>

		<h2>Text</h2>
		<textarea
			rows="5"
			cols="80"
			value={text}
			on:input={(e) => {
				input = { type: "text", value: e.currentTarget.value };
			}}
		/><br />
		<button on:click={() => copy(text)}>Copy</button><br /><br />

		<h2>Encoded</h2>
		<textarea
			rows="5"
			cols="80"
			bind:value={encoded}
			on:input={(e) => {
				input = { type: "encoded", value: e.currentTarget.value };
			}}
		/><br />
		<button on:click={() => copy(encoded)}>Copy</button>
	</div>
</div>

<style>
	.secret {
		font-style: italic;
	}
	.options {
		display: flex;
		flex-direction: row;
		gap: 2em;
	}
	.options > * {
		flex-grow: 0;
	}
</style>
