<script setup>
import { onMounted, ref, onUnmounted } from "vue";
import * as monaco from "monaco-editor";
import jsonWorker from "monaco-editor/esm/vs/language/json/json.worker?worker";
import { LaTeXJSComponent } from "https://cdn.jsdelivr.net/npm/latex.js/dist/latex.mjs";
customElements.define("latex-js", LaTeXJSComponent);

const content = ref(null);

const container = ref(null);
let editor;

// @ts-ignore
self.MonacoEnvironment = {
	getWorker(_, label) {
		return new jsonWorker();
	},
};

monaco.languages.typescript.typescriptDefaults.setEagerModelSync(true);

onMounted(() => {
	editor = monaco.editor.create(container.value, {
		language: "latex",
		theme: "vs-dark",
	});
});

onUnmounted(() => {
	editor?.dispose();
});

function preview() {
	content.value = null;

	let val = editor.getValue();

	content.value = val;
}

function download() {
	console.log("todo");
}
</script>

<template>
	<div class="flex-col h-screen overflow-hidden">
		<div
			class="flex h-14 w-full px-4 bg-gray-50 justify-between items-center text-blue-500"
		>
			<h1 class="font-extrabold text-2xl">LaTeX Web Test</h1>
			<div>
				<button
					@click.prevent="preview"
					class="bg-green-500 py-2 px-4 m-2 rounded text-white"
				>
					Preview
				</button>
				<button
					@click.prevent="download"
					class="bg-blue-500 py-2 px-4 m-2 rounded text-white"
				>
					Download PDF (TODO)
				</button>
			</div>
		</div>
		<div class="flex flex-1">
			<div ref="container" class="flex-1 h-screen"></div>
			<div class="flex-1 h-100 overflow-hidden mb-20">
				<latex-js v-if="content" class="inline-block h-screen overflow-auto">
					{{ content }}
				</latex-js>
			</div>
		</div>
	</div>
</template>
