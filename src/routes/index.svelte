<script lang="ts">
	import Blockly from 'blockly/core';
	
	import En from 'blockly/msg/en';
	import De from 'blockly/msg/de';
	import 'blockly/blocks';
	import 'blockly/javascript';

	import BlocklyComponent, { Locale, Transform } from '../lib';

	const en: Locale = {
		rtl: false,
		msg: {
			CAT_LOGIC: 'Logic',
			CAT_LOOPS: 'Loops',
			CAT_MATH: 'Math',
			CAT_LISTS: 'Lists',
			CAT_VARIABLES: 'Variables',
			CAT_FUNCTIONS: 'Functions',
			CAT_TEXT: 'Text',
			...En,
		},
	};

	const de: Locale = {
		rtl: false,
		msg: {
			CAT_LOGIC: 'Logik',
			CAT_LOOPS: 'Schleifen',
			CAT_MATH: 'Mathe',
			CAT_LISTS: 'Listen',
			CAT_VARIABLES: 'Variablen',
			CAT_FUNCTIONS: 'Funktionen',
			CAT_TEXT: 'Text',
			...De,
		},
	};

	const locales: Record<string, Locale> = { en, de };

	const toolbox: Blockly.utils.toolbox.ToolboxDefinition = {
		kind: undefined,
		contents: [
			{
				...(undefined as Blockly.utils.toolbox.StaticCategoryInfo),
				kind: 'category',
				name: '%{BKY_CAT_LOGIC}',
				colour: '%{BKY_LOGIC_HUE}',
				contents: [
					{ kind: 'block', type: 'controls_if' },
					{
						kind: 'block',
						blockxml: `
							<block type="controls_if">
								<mutation else="1" />
							</block>`,
					},
					{
						kind: 'block',
						blockxml: `
							<block type="controls_if">
								<mutation elseif="1" else="1" />
							</block>`,
					},
					{
						kind: 'block',
						blockxml: `
							<block type="controls_for">
								<field name="VAR">i</field>
								<value name="FROM">
									<block type="math_number">
										<field name="NUM">1</field>
									</block>
								</value>
								<value name="TO">
									<block type="math_number">
										<field name="NUM">10</field>
									</block>
								</value>
								<value name="BY">
									<block type="math_number">
										<field name="NUM">1</field>
									</block>
								</value>
							</block>`,
					},
					{ kind: 'block', type: 'logic_compare' },
					{ kind: 'block', type: 'logic_operation' },
					{ kind: 'block', type: 'logic_negate' },
					{ kind: 'block', type: 'logic_boolean' },
					{ kind: 'block', type: 'logic_null' },
					{ kind: 'block', type: 'logic_ternary' },
				] as Blockly.utils.toolbox.BlockInfo[],
			},
			{
				...(undefined as Blockly.utils.toolbox.StaticCategoryInfo),
				kind: 'category',
				name: '%{BKY_CAT_LOOPS}',
				colour: '%{BKY_LOOPS_HUE}',
				contents: [
					{ kind: 'block', type: 'controls_repeat_ext' },
					{ kind: 'block', type: 'controls_whileUntil' },
					{
						kind: 'block',
						blockxml: `
							<block type="controls_for">
								<field name="VAR">i</field>
								<value name="FROM">
									<block type="math_number">
										<field name="NUM">1</field>
									</block>
								</value>
								<value name="TO">
									<block type="math_number">
										<field name="NUM">10</field>
									</block>
								</value>
								<value name="BY">
									<block type="math_number">
										<field name="NUM">1</field>
									</block>
								</value>
							</block>`,
					},
					{ kind: 'block', type: 'controls_forEach' },
					{ kind: 'block', type: 'controls_flow_statements' },
				] as Blockly.utils.toolbox.BlockInfo[],
			},
			{
				...(undefined as Blockly.utils.toolbox.StaticCategoryInfo),
				kind: 'category',
				name: '%{BKY_CAT_MATH}',
				colour: '%{BKY_MATH_HUE}',
				contents: [
					{ kind: 'block', type: 'math_number' },
					{ kind: 'block', type: 'math_single' },
					{ kind: 'block', type: 'math_trig' },
					{ kind: 'block', type: 'math_constant' },
					{ kind: 'block', type: 'math_number_property' },
					{ kind: 'block', type: 'math_round' },
					{ kind: 'block', type: 'math_on_list' },
					{ kind: 'block', type: 'math_modulo' },
					{ kind: 'block', type: 'math_constrain' },
					{ kind: 'block', type: 'math_random_int' },
					{ kind: 'block', type: 'math_random_float' },
					{ kind: 'block', type: 'math_atan2' },
				] as Blockly.utils.toolbox.BlockInfo[],
			},
			{
				...(undefined as Blockly.utils.toolbox.StaticCategoryInfo),
				kind: 'category',
				name: '%{BKY_CAT_LISTS}',
				colour: '%{BKY_LISTS_HUE}',
				contents: [
					{ kind: 'block', type: 'lists_create_empty' },
					{ kind: 'block', type: 'lists_create_with' },
					{ kind: 'block', type: 'lists_repeat' },
					{ kind: 'block', type: 'lists_length' },
					{ kind: 'block', type: 'lists_isEmpty' },
					{ kind: 'block', type: 'lists_indexOf' },
					{ kind: 'block', type: 'lists_getIndex' },
					{ kind: 'block', type: 'lists_setIndex' },
				] as Blockly.utils.toolbox.BlockInfo[],
			},
			{ kind: 'sep' } as Blockly.utils.toolbox.SeparatorInfo,
			{
				...(undefined as Blockly.utils.toolbox.StaticCategoryInfo),
				kind: 'category',
				name: '%{BKY_CAT_VARIABLES}',
				custom: 'VARIABLE',
				colour: '%{BKY_VARIABLES_HUE}',
			},
			{
				...(undefined as Blockly.utils.toolbox.StaticCategoryInfo),
				kind: 'category',
				name: '%{BKY_CAT_FUNCTIONS}',
				custom: 'PROCEDURE',
				colour: '%{BKY_PROCEDURES_HUE}',
			},
			{
				...(undefined as Blockly.utils.toolbox.StaticCategoryInfo),
				kind: 'category',
				name: '%{BKY_CAT_TEXT}',
				colour: 70,
				contents: [
					{ kind: 'block', type: 'text' } as Blockly.utils.toolbox.BlockInfo,
					{
						...(undefined as Blockly.utils.toolbox.BlockInfo),
						kind: 'block',
						blockxml: `<block type="text_join" inline="true" />`,
					},
				],
			},
		],
	};

	const config = {
		toolbox,
		move: {
			scrollbars: true,
			drag: true,
			wheel: false,
		},
		zoom: {
			controls: false,
			wheel: true,
			maxScale: 1.5,
			minScale: 0.4,
			scaleSpeed: 1.02,
		},
		grid: {
			spacing: 20,
			length: 3,
			colour: '#ccc',
			snap: true,
		},
		trashcan: false,
	};

	let locale = 'en';
	let transform: Transform;
	let workspace: Blockly.WorkspaceSvg;

	let saved: [string, Transform] | undefined = undefined;
	let code = '';

	function handleSave() {
		const xml = Blockly.Xml.domToPrettyText(Blockly.Xml.workspaceToDom(workspace));
		saved = [xml, transform];
	}

	function handleRestore() {
		Blockly.Xml.clearWorkspaceAndLoadFromXml(Blockly.Xml.textToDom(saved[0]), workspace);
		transform = saved[1];
	}

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

<main>
	<div>
		<h1>Config</h1>
		<div>
			<select bind:value={locale}>
				{#each Object.keys(locales) as locale}
					<option value={locale}>{locale}</option>
				{/each}
			</select>
			<button on:click={handleSave}>Save Editor</button>
			<button on:click={handleRestore} disabled={saved === undefined}>Restore Editor</button>
		</div>
		<h2>Current JS Code & Transform</h2>
		<p>{JSON.stringify(transform)}</p>
		<pre>{code}</pre>
		<h2>Saved Blockly XML & transform</h2>
		{#if saved !== undefined}
			<p>{JSON.stringify(saved[1])}</p>
			<pre>{saved[0]}</pre>
		{:else}
			<p>(none)</p>
		{/if}
	</div>
	<div>
		<h1>Demo</h1>
		<div class="blockly-container">
			<BlocklyComponent
				{config}
				locale={locales[locale]}
				bind:workspace
				bind:transform
				on:change={onChange}
			/>
		</div>
	</div>
</main>

<style>
	main {
		display: flex;
		flex-direction: row;
	}

	main > div {
		width: 50%;
		padding: 2rem;
	}

	.blockly-container {
		height: 600px;

		border: 1px solid black;
	}

	pre {
		overflow-x: auto;
	}
</style>
