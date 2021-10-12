# svelte-blockly

[Blockly](https://developers.google.com/blockly/) Wrapper for Svelte. The `Blockly` component does roughly the work of [`Blockly.inject()`], plus some handling of localization and transform and change tracking.

This was extracted from a personal project, so its initial scope was rather limited. Expect rough edges, but bug reports and contributions are welcome!

**Features**

- configuration like [`Blockly.inject()`]
- automatic workspace refresh on configuration & locale changes
- reactive transform prop - change or monitor the workspace transform
- change event
- automatic resizing of the layout within its containing HTML element

## Installation & development

An **npm package** does not exist yet. If reception is good, I will publish this and develop it further.

```sh
# clone this repo and package the library
git clone https://github.com/SillyFreak/svelte-blockly
cd svelte-blockly
npm i
npm run package

# add it & golden-layout to your own project
cd ../your-own-project
npm i blockly ../svelte-blockly/package
```

## simple example

In this example, `svelte:component` is used to select a specific component for each tab, and the `componentState` object is passed as props to that component:

```svelte
<script lang="ts">
	import Blockly from 'blockly/core';
	
	import En from 'blockly/msg/en';
	import 'blockly/blocks';
	import 'blockly/javascript';

	import BlocklyComponent, { Locale, Transform } from 'svelte-blockly';

	const en: Locale = {
		rtl: false,
		msg: {
			...En,
			...
		},
	};

	const config = {
		toolbox: ...,
		...
	};

	let workspace: Blockly.WorkspaceSvg;
	let code = '';

	function onChange() {
		// eslint-disable-next-line @typescript-eslint/no-explicit-any
		const lang = (Blockly as any)['JavaScript'];
		try {
			code = lang.workspaceToCode(workspace);
		} catch (_err) {
			// Happens e.g. when deleting a function that is used somewhere.
			// Blockly will quickly recover from this, so it's not a big deal.
			// Just make sure the app doesn't crash until then.
		}
	}
</script>

<div class="blockly-container">
	<BlocklyComponent
		{config}
		locale={en}
		bind:workspace
		on:change={onChange}
	/>
</div>
<pre>{code}</pre>

<style>
	.blockly-container {
		height: 600px;

		border: 1px solid black;
	}

	pre {
		overflow-x: auto;
	}
</style>
```

You can also run this repo as an app; the example code is in [routes/index.svelte](src/routes/index.svelte) demonstates the features of the library.
