<script lang="ts">
	import { classNames } from '$lib/utils'
	import { getContext } from 'svelte'
	import type { Output } from '../../rx'
	import type { AppViewerContext } from '../../types'
	import { connectInput } from '../appUtils'
	import ComponentOutput from './ComponentOutput.svelte'

	export let name: string | undefined = undefined
	export let parentId: string
	export let expanded: boolean = false
	export let subGrids: string[]

	const { app, connectingInput, worldStore } = getContext<AppViewerContext>('AppViewerContext')

	let selected = 0

	$: outputs = $worldStore?.outputsById[parentId] as {
		selectedTabIndex: Output<number>
	}

	$: subgridItems = subGrids.map((k) => ({
		k,
		items: $app.subgrids?.[k] ?? []
	}))

	$: if (outputs?.selectedTabIndex) {
		subscribeToOutput()
	}

	function subscribeToOutput() {
		outputs.selectedTabIndex.subscribe(
			{
				id: 'subgridoutput-' + parentId,
				next: (value) => {
					selected = value
				}
			},
			selected
		)
	}
</script>

{#each subgridItems as { k, items }, index (k)}
	<div class="ml-2 my-2">
		{#if subGrids.length > 1}
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<div
				class={classNames(
					'px-1 py-0.5 flex justify-between items-center font-semibold text-xs border-l border-y w-full cursor-pointer',
					selected === index ? 'bg-surface-selected' : 'bg-surface'
				)}
				on:click={() => {
					selected = index
				}}
			>
				<div class="text-xs">
					{name ? name : 'Should implement name'}
					{index + 1}
				</div>
			</div>
		{/if}

		{#if selected === index || name !== 'Tabs'}
			<div class="border-l">
				{#if items.length > 0}
					{#each items as subGridItem, index (subGridItem.id)}
						<ComponentOutput
							gridItem={subGridItem}
							first={index === 0}
							{expanded}
							on:select={({ detail }) => {
								$connectingInput = connectInput($connectingInput, subGridItem.id, detail)
							}}
						/>
					{/each}
				{:else}
					<div class="text-xs text-tertiary border-y border-l p-1">No components</div>
				{/if}
			</div>
		{/if}
	</div>
{/each}
