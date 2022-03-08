<script setup>
import { onMounted, ref, onUnmounted, nextTick } from "vue";
import * as monaco from "monaco-editor";
import jsonWorker from "monaco-editor/esm/vs/language/json/json.worker?worker";
import { LaTeXJSComponent } from "https://cdn.jsdelivr.net/npm/latex.js/dist/latex.mjs";

customElements.define("latex-js", LaTeXJSComponent);
const content = ref(null);
const container = ref(null);
let editor;

self.MonacoEnvironment = {
	getWorker(_, label) {
		return new jsonWorker();
	},
};

// Register a new language
monaco.languages.register({ id: "latex" });

// Register a tokens provider for the language
monaco.languages.setMonarchTokensProvider("latex", {
	lineComment: "%",
	builtin: [
		"addcontentsline",
		"addtocontents",
		"addtocounter",
		"address",
		"addtolength",
		"addvspace",
		"alph",
		"appendix",
		"arabic",
		"author",
		"backslash",
		"baselineskip",
		"baselinestretch",
		"bf",
		"bibitem",
		"bigskipamount",
		"bigskip",
		"boldmath",
		"boldsymbol",
		"cal",
		"caption",
		"cdots",
		"centering",
		"chapter",
		"circle",
		"cite",
		"cleardoublepage",
		"clearpage",
		"cline",
		"closing",
		"color",
		"copyright",
		"dashbox",
		"date",
		"ddots",
		"documentclass",
		"dotfill",
		"em",
		"emph",
		"ensuremath",
		"epigraph",
		"euro",
		"fbox",
		"flushbottom",
		"fnsymbol",
		"footnote",
		"footnotemark",
		"footnotesize",
		"footnotetext",
		"frac",
		"frame",
		"framebox",
		"frenchspacing",
		"hfill",
		"hline",
		"href",
		"hrulefill",
		"hspace",
		"huge",
		"Huge",
		"hyphenation",
		"include",
		"includegraphics",
		"includeonly",
		"indent",
		"input",
		"it",
		"item",
		"kill",
		"label",
		"large",
		"Large",
		"LARGE",
		"LaTeX",
		"LaTeXe",
		"ldots",
		"left",
		"lefteqn",
		"line",
		"linebreak",
		"linethickness",
		"linewidth",
		"listoffigures",
		"listoftables",
		"location",
		"makebox",
		"maketitle",
		"markboth",
		"mathcal",
		"mathop",
		"mbox",
		"medskip",
		"multicolumn",
		"multiput",
		"newcommand",
		"newcolumntype",
		"newcounter",
		"newenvironment",
		"newfont",
		"newlength",
		"newline",
		"newpage",
		"newsavebox",
		"newtheorem",
		"nocite",
		"noindent",
		"nolinebreak",
		"nonfrenchspacing",
		"normalsize",
		"nopagebreak",
		"not",
		"onecolumn",
		"opening",
		"oval",
		"overbrace",
		"overline",
		"pagebreak",
		"pagenumbering",
		"pageref",
		"pagestyle",
		"par",
		"paragraph",
		"parbox",
		"parindent",
		"parskip",
		"part",
		"protect",
		"providecommand",
		"put",
		"raggedbottom",
		"raggedleft",
		"raggedright",
		"raisebox",
		"ref",
		"renewcommand",
		"right",
		"rm",
		"roman",
		"rule",
		"savebox",
		"sbox",
		"sc",
		"scriptsize",
		"section",
		"setcounter",
		"setlength",
		"settowidth",
		"sf",
		"shortstack",
		"signature",
		"sl",
		"slash",
		"small",
		"smallskip",
		"sout",
		"space",
		"sqrt",
		"stackrel",
		"stepcounter",
		"subparagraph",
		"subsection",
		"subsubsection",
		"tableofcontents",
		"telephone",
		"TeX",
		"textbf",
		"textcolor",
		"textit",
		"textmd",
		"textnormal",
		"textrm",
		"textsc",
		"textsf",
		"textsl",
		"texttt",
		"textup",
		"textwidth",
		"textheight",
		"thanks",
		"thispagestyle",
		"tiny",
		"title",
		"today",
		"tt",
		"twocolumn",
		"typeout",
		"typein",
		"uline",
		"underbrace",
		"underline",
		"unitlength",
		"usebox",
		"usecounter",
		"uwave",
		"value",
		"vbox",
		"vcenter",
		"vdots",
		"vector",
		"verb",
		"vfill",
		"vline",
		"vphantom",
		"vspace",

		"RequirePackage",
		"NeedsTeXFormat",
		"usepackage",
		"input",
		"include",
		"documentclass",
		"documentstyle",
		"def",
		"edef",
		"defcommand",
		"if",
		"ifdim",
		"ifnum",
		"ifx",
		"fi",
		"else",
		"begingroup",
		"endgroup",
		"definecolor",
		"textcolor",
		"color",
		"eifstrequal",
		"eeifstrequal",
	],
	tokenizer: {
		root: [
			[/\{[a-zA-Z 0-9:]+\}/, "special"],
			[
				"(\\\\begin)(\\s*)(\\{)([\\w\\-\\*\\@]+)(\\})",
				[
					"keyword.predefined",
					"white",
					"@brackets",
					{ token: "tag.env-$4", bracket: "@open" },
					"@brackets",
				],
			],
			[
				"(\\\\end)(\\s*)(\\{)([\\w\\-\\*\\@]+)(\\})",
				[
					"keyword.predefined",
					"white",
					"@brackets",
					{ token: "tag.env-$4", bracket: "@close" },
					"@brackets",
				],
			],
			["\\\\[^a-zA-Z@]", "keyword"],
			["\\@[a-zA-Z@]+", "keyword.at"],
			[
				"\\\\([a-zA-Z@]+)",
				{
					cases: {
						"$1@builtin": "keyword.predefined",
						"@default": "keyword",
					},
				},
			],
			{ include: "@whitespace" },
			["[{}()\\[\\]]", "@brackets"],
			["#+\\d", "number.arg"],
			[
				"\\-?(?:\\d+(?:\\.\\d+)?|\\.\\d+)\\s*(?:em|ex|pt|pc|sp|cm|mm|in)",
				"number.len",
			],
		],

		whitespace: [
			["[ \\t\\r\\n]+", "white"],
			["%.*$", "comment"],
		],
	},
});

// Define a new theme that contains only rules that match this language
monaco.editor.defineTheme("latex-theme", {
	base: "vs",
	inherit: true,
	rules: [
		{ token: "special", foreground: "#ae81ff" },
		{ token: "brackets", foreground: "#000000" },
		{ token: "keyword", foreground: "#f92672" },
	],
	colors: {},
});

onMounted(() => {
	editor = monaco.editor.create(container.value, {
		theme: "latex-theme",
		language: "latex",
	});

	editor.addCommand(monaco.KeyMod.CtrlCmd | monaco.KeyCode.KeyS, function () {
		preview();
	});
});

onUnmounted(() => {
	editor?.dispose();
});

const renderComponent = ref(true);
async function forceRerender() {
	renderComponent.value = false;
	await nextTick();
	renderComponent.value = true;
}

function preview() {
	forceRerender();

	let val = editor.getValue();

	content.value = val;
}

function download() {
	console.log("todo");
}
</script>

<template>
	<div class="flex-col h-screen overflow-auto">
		<div
			class="flex w-full py-2 px-4 bg-emerald-50 justify-between items-center"
		>
			<h1 class="font-extrabold text-2xl">LaTeX Web Test</h1>
			<div>
				<button
					@click.prevent="preview"
					class="bg-green-500 py-2 px-4 m-2 rounded text-white"
				>
					Preview [ctrl + s]
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
			<div ref="container" class="flex-1 h-screen w-full"></div>
			<div class="flex-1 h-100 overflow-hidden">
				<latex-js
					v-if="renderComponent"
					class="inline-block h-screen overflow-auto"
				>
					{{ content }}
				</latex-js>
			</div>
		</div>
	</div>
</template>
