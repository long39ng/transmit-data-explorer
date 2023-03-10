<script lang="ts">
	import type { ChangeEventHandler } from "./utils";
	import Icon from "svelte-icon/Icon.svelte";
	import hamburger from "$lib/assets/hamburger.svg?raw";
	import logo from "$lib/assets/logo.png";
	import ChartPanel from "./ChartPanel.svelte";
	import { data } from "$lib/data";

	const responseVars = Object.keys(data);
	let currResponse = responseVars[0];

	let facetVars: string[];
	let currFacetVar: string;
	let prevFacetVar: string;

	let colourVars: string[];
	let currColourVar: string;
	let prevColourVar: string;

	$: {
		if (currResponse) {
			facetVars = Object.keys(data[currResponse]);
			currFacetVar = facetVars.includes(prevFacetVar) ? prevFacetVar : facetVars[0];

			colourVars = Object.keys(data[currResponse][currFacetVar]);
			currColourVar = colourVars.includes(prevColourVar) ? prevColourVar : colourVars[0];

			prevFacetVar = currFacetVar;
			prevColourVar = currColourVar;
		}
	}

	function handleFacetChange(event: ChangeEventHandler<HTMLSelectElement>) {
		prevFacetVar = currFacetVar;
		currFacetVar = event.currentTarget.value;
	}

	function handleColourChange(event: ChangeEventHandler<HTMLSelectElement>) {
		prevColourVar = currColourVar;
		currColourVar = event.currentTarget.value;
	}

	let showPercentages = false;
	$: if (currResponse === "plan_mig") showPercentages = false;
</script>

<div class="drawer-mobile drawer">
	<input id="page-drawer" type="checkbox" class="drawer-toggle" />

	<div class="drawer-content flex flex-col p-4">
		<span class="mb-4 flex items-center before:text-xs before:content-[attr(data-tip)] lg:hidden">
			<label for="page-drawer" class="btn-ghost drawer-button btn-square btn mr-4">
				<Icon data={hamburger} />
			</label>
			<h1 class="text-lg font-bold uppercase">{currResponse}</h1>
		</span>

		<div class="mb-8">
			<label class="cursor-pointer">
				<span class="label-text">Groups</span>
				<select
					class="select-bordered select select-sm m-1 mr-4"
					bind:value={currFacetVar}
					on:change={handleFacetChange}
				>
					{#each Object.keys(data[currResponse]) as facetBy}
						<option value={facetBy}>{facetBy}</option>
					{/each}
				</select>
			</label>

			<label class="cursor-pointer">
				<span class="label-text">Colour</span>
				<select
					class="select-bordered select select-sm m-1 mr-4"
					bind:value={currColourVar}
					on:change={handleColourChange}
				>
					{#each Object.keys(data[currResponse][currFacetVar]) as colourBy}
						<option value={colourBy}>{colourBy}</option>
					{/each}
				</select>
			</label>

			<br class="lg:hidden" />

			<label class={currResponse === "plan_mig" ? "hidden cursor-pointer" : "cursor-pointer"}>
				<span class="label-text">Show percentages</span>
				<input type="checkbox" class="toggle m-1 align-middle" bind:checked={showPercentages} />
			</label>
		</div>

		<ChartPanel data={data[currResponse][currFacetVar][currColourVar]} {showPercentages} />
	</div>

	<div class="drawer-side">
		<label for="page-drawer" class="drawer-overlay" />

		<div class="menu w-80 bg-base-100 p-4 pr-8 text-base-content">
			<div class="p-4">
				<img src={logo} alt="TRANSMIT Project logo" />
			</div>

			<div class="mt-4">
				{#each responseVars as response}
					<button
						class="btn-ghost btn-sm btn m-1 block w-full text-left"
						class:btn-active={currResponse === response}
						on:click={() => (currResponse = response)}>{response}</button
					>
				{/each}
			</div>
		</div>
	</div>
</div>
