<script lang="ts">
	import rita from 'rita';

	let fileContent: string | ArrayBuffer | null;
	let loaded = false;
	let lines: string;

	let n = 3;
	let temperature = 0.1;
	let nSentences = 2;

	function handleFileUpload(event: any) {
		const file = event.target.files[0];
		const reader = new FileReader();
		reader.onload = () => {
			fileContent = reader.result;
		};
		reader.readAsText(file);
		loaded = true;
	}

	function generatePoem() {
		try {
			const rm = new rita.RiMarkov(n);
			rm.addText(fileContent);
			lines = rm.generate(nSentences, { temperature: temperature });
		} catch (e) {
			alert("Couldn't generate anything! Try lower n-value and/or number of lines");
		}
	}
</script>

<div>
	<h1>Poke.Markov</h1>
	<p>
		PokeMarkov is an experimental approach to generative writing. It uses Markov chains (n-gram
		models) to produce the text based on an arbitrary input, which can anything: poetry, prose,
		dialogue, etc. Keep in mind that this is a "naive" approach that doesn't consider rhyming
		patterns or forms. And it can be quite messy.
	</p>
	<p>First, upload the corpus (Only .txt files are supported).</p>
	<div class="input-wrapper">
		<input type="file" accept=".txt" id="file-upload" on:change={handleFileUpload} />
	</div>
	{#if loaded}
		<p>
			Now, enter the parameters.
			<br />
			<b>n-value</b> determines the number of elements to consider
			<br />
			Higher <b>temperature</b> means that less frequent elements will appear more often
		</p>
		<div class="controls-wrapper">
			<div>
				<label for="input-n">n-value</label>
				<input type="number" id="input-n" min="2" bind:value={n} />
			</div>
			<div>
				<label for="input-nSentences">Number of sentences</label>
				<input type="number" id="input-nSentences" min="2" bind:value={nSentences} />
			</div>
			<div>
				<label for="input-temperature">Temperature</label>
				<input
					type="range"
					min="0.1"
					max="2048"
					step="0.1"
					id="input-temperature"
					bind:value={temperature}
				/>
				<div>{temperature}</div>
			</div>
		</div>
		<p>
			<button id="markov-talk" disabled={!loaded} on:click={generatePoem}>Generate</button>
		</p>
	{/if}
	{#if lines != null}
		<div>
			<div>
				<p>
					{#each lines as line}
						{line}<br />
					{/each}
				</p>
			</div>
		</div>
	{/if}
</div>

<style>
	@font-face {
		font-family: 'Playfair Display';
		font-style: normal;
		font-weight: 500;
		src: url('PlayfairDisplay-Regular.ttf');
		src: local(''), url('PlayfairDisplay-Regular.ttf') format('truetype');
	}

	@font-face {
		font-family: 'Playfair Display';
		font-style: normal;
		font-weight: 600;
		src: url('PlayfairDisplay-Medium.ttf');
		src: local(''), url('PlayfairDisplay-Medium.ttf') format('truetype');
	}

	p,
	input {
		font-family: 'Playfair Display', 'Times New Roman', Times, serif;
		font-weight: 500;
	}
	p {
		text-align: center;
		width: 50%;
		margin-left: auto;
		margin-right: auto;
		font-size: 1.125rem;
		line-height: 1.75rem;
		margin-top: 1.25rem;
		font-size: large;
	}

	h1 {
		font-family: 'Playfair Display', 'Times New Roman', Times, serif;
		font-weight: 600;
		font-size: xx-large;
		text-align: center;
	}

	.input-wrapper {
		display: flex;
		justify-content: center;
		text-align: center;
		margin-left: auto;
		margin-right: auto;
		margin-top: 1.25rem;
	}

	.controls-wrapper {
		padding-top: 1.25rem;
		margin-left: 2.5rem;
		margin-right: 2.5rem;
	}

	.controls-wrapper > div {
		display: flex;
		justify-content: center;
		padding-bottom: 1.25rem;
	}

	.controls-wrapper > div > label {
		padding-right: 1.25rem;
		font-weight: 600;
	}
</style>
