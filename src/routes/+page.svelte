<script lang="ts">
	import { FileButton, RadioGroup, RadioItem } from '@skeletonlabs/skeleton';
	import { writable } from 'svelte/store';
	//
	let audioUrl: any;
	let file: any;
	let audioElement: any;
	let track: any;
	let value: number = 20;
	let eq = writable<any>(null);
	let audioCtx: any;
	// onMount(() => {});

	const createEq = (age: number = 20) => {
		let freq = 0;

		switch (age) {
			case 20:
				freq = 20000;
				break;
			case 70:
				freq = 10000;
				break;
			case 80:
				freq = 1000;
				break;
		}
		const eQ = new BiquadFilterNode(audioCtx, { frequency: freq, type: 'lowpass' });
		// console.log(eQ.frequency);
		// track.connect(eq).connect(audioCtx.destination);
		eq.set(eQ);
		// console.log($eq);
	};

	const onUpload = (e: any = null) => {
		// console.log('audiourl', audioUrl);
		// if (audioCtx) audioCtx.close();
		if (!audioCtx) audioCtx = new window.AudioContext();
		createEq();
		if (audioUrl) URL.revokeObjectURL(audioUrl);
		let file;
		file = e.target.files[0];
		if (!track) {
			track = audioCtx.createMediaElementSource(audioElement);
		}

		audioUrl = URL.createObjectURL(file);
		audioElement.src = audioUrl;
		reconnectAudioNodes();
	};
	const reconnectAudioNodes = () => {
		if (!track) return;
		track.disconnect();
		createEq(value);
		track.connect($eq).connect(audioCtx.destination);
	};
</script>

<div class="container h-full mx-auto flex flex-col gap-10 justify-center items-center">
	<div class="card p-10 gap-10 flex flex-col">
		<form class="space-y-10 text-center flex flex-col items-center">
			<h1 class="h3 uppercase">Upload Audio File</h1>
			<FileButton
				name="audio-input"
				type="file"
				bind:value={file}
				on:change={onUpload}
				accept="audio/*"
			/>
		</form>
		<!-- {#if audioUrl} -->
		<audio bind:this={audioElement} controls />
		<h2 class="h4 text-center uppercase">Low-Pass</h2>
		<RadioGroup active="variant-filled-primary" hover="hover:variant-soft-primary">
			<RadioItem on:change={() => reconnectAudioNodes()} name="age" bind:group={value} value={20}
				>20,000hz</RadioItem
			>
			<RadioItem on:change={() => reconnectAudioNodes()} name="age" bind:group={value} value={70}
				>10,000hz</RadioItem
			>
			<RadioItem on:change={() => reconnectAudioNodes()} name="age" bind:group={value} value={80}
				>1000hz</RadioItem
			>
		</RadioGroup>
	</div>

	<!-- {/if} -->
</div>
