<script lang="ts">
	import { getContext } from 'svelte'
	import type { FlowEditorContext } from '../flows/types'
	import Tutorial from './Tutorial.svelte'
	import { RawScript } from '$lib/gen/models/RawScript'
	import { clickButtonBySelector } from './utils'
	import { updateProgress } from '$lib/tutorialUtils'

	const { flowStore } = getContext<FlowEditorContext>('FlowEditorContext')

	let tutorial: Tutorial | undefined = undefined

	export function runTutorial() {
		tutorial?.runTutorial()
	}
</script>

<Tutorial
	bind:this={tutorial}
	index={0}
	name="error-handler"
	tainted={false}
	on:error
	on:skipAll
	getSteps={(driver) => [
		{
			popover: {
				title: 'Welcome to the Windmil Flow editor',
				description: 'Learn how to recover from an error. You can use arrow keys to navigate.',
				onNextClick: () => {
					$flowStore.value.modules = [
						{
							id: 'a',
							value: {
								type: 'rawscript',
								content:
									'// import * as wmill from "npm:windmill-client@1"\n\nexport async function main(x: string) {\n  throw new Error("Fake error")\n}\n',
								language: RawScript.language.DENO,
								input_transforms: {
									x: {
										type: 'static',
										value: ''
									}
								}
							}
						}
					]
					setTimeout(() => {
						driver.moveNext()
					})
				}
			}
		},
		{
			element: '#error-handler-toggle',
			popover: {
				title: 'Error handler',
				description:
					'You can add an error handler to your flow. It will be executed if any of the steps in the flow fails.',

				onNextClick: () => {
					clickButtonBySelector('#error-handler-toggle')
					setTimeout(() => {
						driver.moveNext()
					})
				}
			}
		},
		{
			popover: {
				title: 'Steps kind',
				description: "Choose the kind of step you want to add. Let's start with a simple action"
			},
			element: '#flow-editor-insert-module'
		},
		{
			element: '#flow-editor-flow-inputs',
			popover: {
				title: 'Action configuration',
				description: 'An action can be inlined, imported from your workspace or the Hub.'
			}
		},
		{
			element: '#flow-editor-action-script',
			popover: {
				title: 'Supported languages',
				description: 'Windmill support the following languages/runtimes.'
			}
		},
		{
			element: '#flow-editor-action-script > button:nth-child(1)',
			popover: {
				title: 'Typescript',
				description: "Let's pick an action to add to your flow",
				onNextClick: () => {
					clickButtonBySelector('#flow-editor-action-script > button > div > button:nth-child(1)')

					setTimeout(() => {
						driver.moveNext()
					})
				}
			}
		},
		{
			element: '#flow-editor-test-flow',
			popover: {
				title: 'Test your flow',
				description: 'We can now test our flow',
				onNextClick: () => {
					clickButtonBySelector('#flow-editor-test-flow')

					setTimeout(() => {
						driver.moveNext()
					})
				}
			}
		},

		{
			element: '#flow-editor-test-flow-drawer',
			popover: {
				title: 'Test your flow',
				description:
					'Finally we can test our flow, and view how the error handler is executed when a step fails',
				onNextClick: () => {
					clickButtonBySelector('#flow-editor-test-flow-drawer')

					setTimeout(() => {
						driver.moveNext()

						updateProgress(4)
					})
				}
			}
		}
	]}
/>
