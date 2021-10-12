<script lang="ts" context="module">
	export type Transform = {
		scrollX: number;
		scrollY: number;
		scale: number;
	};

	export type Locale = {
		rtl: boolean;
		msg: Record<string, string>;
	};
</script>

<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import Blockly from 'blockly/core';

	export let config: Blockly.BlocklyOptions = {};
	export let locale: Locale;

	export let workspace: Blockly.WorkspaceSvg = undefined;
	export let transform: Transform = undefined;

	$: {
		// evaluate transform to establish a reactive dependency
		transform;

		applyTransform();
	}

	const dispatch = createEventDispatcher();
	let width: number, height: number;

	type InjectParams = {
		config: Blockly.BlocklyOptions;
		locale: Locale;
	};

	function initRoot(root: HTMLDivElement, param: InjectParams) {
		function injectWorkspace(dom: Element | null, { config, locale: { msg, rtl } }: InjectParams) {
			Blockly.setLocale(msg);
			workspace = Blockly.inject(root, {
				...config,
				rtl,
			});

			if (dom !== null) {
				try {
					// don't record this reloading of the workspace for undo
					Blockly.Events.recordUndo = false;

					Blockly.Xml.clearWorkspaceAndLoadFromXml(dom, workspace);

					Blockly.Events.recordUndo = true;
				} catch (ex) {
					console.warn(ex);
				}
			}

			workspace.addChangeListener(() => {
				dispatch('change');
			});

			// TODO this is a terrible hack, but there's no scroll event
			// translate is the most fundamental in a set of methods
			// that move and zoom the workspace
			// using an arrow function, so `this` is the component not the workspace
			const translate = workspace.translate.bind(workspace);
			workspace.translate = (x, y) => {
				translate(x, y);
				const { scrollX, scrollY, scale } = workspace;
				transform = { scrollX, scrollY, scale };
			};

			if (transform !== undefined) {
				applyTransform();
			} else {
				const { scrollX, scrollY, scale } = workspace;
				transform = { scrollX, scrollY, scale };
			}
		}

		injectWorkspace(null, param);

		return {
			update(param: InjectParams) {
				const dom = Blockly.Xml.workspaceToDom(workspace);
				workspace.dispose();
				injectWorkspace(dom, param);
			},

			destroy() {
				workspace.dispose();
				workspace = undefined;
			},
		};
	}

	function applyTransform() {
		if (workspace === undefined) return;

		const { scrollX, scrollY, scale } = transform;
		if (scrollX !== workspace.scrollX || scrollY !== workspace.scrollY || scale !== workspace.scale) {
			workspace.setScale(scale);
			workspace.scroll(scrollX, scrollY);
		}
	}

	$: {
		// evaluate width & height to establish a reactive dependency
		width;
		height;

		if (workspace) {
			Blockly.svgResize(workspace);
		}
	}

	// TODO this breaks smooth scrolling; for now, transform is read-only
	// $: {
	// 	if (workspace && transform) {
	// 		const { scrollX, scrollY, scale } = transform;
	// 		workspace.scroll(scrollX, scrollY);
	// 		workspace.setScale(scale);
	// 	}
	// }
</script>

<div
	class="root"
	bind:offsetWidth={width}
	bind:offsetHeight={height}
	use:initRoot={{ config, locale }}
/>

<style>
	.root {
		width: 100%;
		height: 100%;
	}
</style>
