<script lang="ts">
	import { ZapIcon, ZapOffIcon } from 'lucide-svelte'
	import Button from '../common/button/Button.svelte'
	import { codeCompletionLoading, copilotInfo, codeCompletionSessionEnabled } from '$lib/stores'
	import Popover from '../Popover.svelte'

	function loadCodeCompletionSessinoEnabled() {
		let stored
		try {
			stored = localStorage.getItem('codeCompletionSessionEnabled')
		} catch (e) {
			console.error('error interacting with local storage', e)
		}

		if (stored) {
			$codeCompletionSessionEnabled = JSON.parse(stored)
		}
	}

	function toggleCodeCompletionSessionEnabled() {
		$codeCompletionSessionEnabled = !$codeCompletionSessionEnabled
		try {
			localStorage.setItem(
				'codeCompletionSessionEnabled',
				JSON.stringify($codeCompletionSessionEnabled)
			)
		} catch (e) {
			console.error('error interacting with local storage', e)
		}
	}

	loadCodeCompletionSessinoEnabled()
</script>

{#if $copilotInfo.exists_openai_resource_path && $copilotInfo.code_completion_enabled}
	<Popover>
		<svelte:fragment slot="text"
			>Click to {$codeCompletionSessionEnabled ? 'disable' : 'enable'} code completion (applies only
			to you)</svelte:fragment
		>
		<Button
			color="light"
			loading={$codeCompletionLoading}
			startIcon={$codeCompletionLoading
				? undefined
				: {
						icon: $codeCompletionSessionEnabled ? ZapIcon : ZapOffIcon
				  }}
			on:click={() => {
				toggleCodeCompletionSessionEnabled()
			}}
		/>
	</Popover>
{/if}
