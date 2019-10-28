<script>
  import Prism from "prismjs";
  import "prismjs/components/prism-markdown";
  import { debounce } from "../utils/debounce";

  let code = "";
  let title = "Document";

  let viewWindow;

  $: styledCode =
    Prism.highlight(code, Prism.languages.markdown, "markdown") + "<br />";

  const debouncedApply = debounce(onApply, 250);

  function openWindow() {
    viewWindow = window.open(
      "/white-board",
      "_blank",
      "toolbar=0,location=0,menubar=0"
    );
  }
  function onApply() {
    viewWindow.sourceObject.content = code;

    viewWindow.sourceObject.title = title;
  }
  /**
   * @param KeyboarEvent event
   */
  function handleKeydown(event) {
    if (!viewWindow || !viewWindow.sourceObject) {
      openWindow();
    }
    if (event.key === "Enter" && (event.metaKey || event.ctrlKey)) {
      onApply();
    } else {
      debouncedApply();
    }
  }
</script>

<style>
  @import url("https://unpkg.com/firacode");
  @import url("https://cdn.jsdelivr.net/gh/PrismJS/prism-themes/themes/prism-ghcolors.css");

  .editor {
    flex: 1;
    position: relative;
    text-align: left;
    box-sizing: border-box;
    padding: 0px;
    overflow: hidden;
    color: #393a34;
    font-family: "Consolas", "Bitstream Vera Sans Mono", "Courier New", Courier,
      monospace;
    border-radius: 0.1em;
    border: 1px solid;
  }
  textarea,
  pre {
    margin: 0px;
    border: 0px;
    background: none;
    box-sizing: inherit;
    display: inherit;
    font-family: inherit;
    font-size: inherit;
    font-style: inherit;
    font-variant-ligatures: inherit;
    font-weight: inherit;
    letter-spacing: inherit;
    line-height: inherit;
    tab-size: inherit;
    text-indent: inherit;
    text-rendering: inherit;
    text-transform: inherit;
    white-space: pre-wrap;
    word-break: keep-all;
    overflow-wrap: break-word;
  }
  textarea {
    position: absolute;
    top: 0px;
    left: 0px;
    height: 100%;
    width: 100%;
    resize: none;
    color: inherit;
    overflow: hidden;
    outline: none;
    -webkit-font-smoothing: antialiased;
    -webkit-text-fill-color: transparent;
    padding: 20px;
  }
  pre {
    position: relative;
    pointer-events: none;
    padding: 20px;
  }
</style>

<svelte:head>
  <title>White Board</title>
</svelte:head>

<button on:click="{openWindow}">Open Window</button>

<label>
  <span>Title</span>
  <input bind:value="{title}" />
</label>
<div class="editor">
  <textarea
    aria-label="source"
    bind:value="{code}"
    on:keydown="{handleKeydown}"></textarea>
  <pre aria-hidden="true">
    {@html styledCode}
  </pre>
</div>

<button on:click="{onApply}">Apply</button>
