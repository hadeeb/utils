<script>
  let code = "";
  let minifiedCode = "";
  $: {
    if (window.Terser) {
      const x = Terser.minify(code);
      if (x.code) {
        minifiedCode = x.code;
      }
    }
  }

  function onCopy() {
    navigator.permissions
      .query({ name: "clipboard-write" })
      .then(({ state }) => {
        if (state === "denied") throw Error("Can't write to clipboard");
      })
      .then(() => {
        return navigator.clipboard.writeText(minifiedCode);
      })
      .then(() => {
        console.log("Copied");
      })
      .catch(err => {
        console.error(err);
        if (err.message) alert(err.message);
      });
  }
</script>

<style>
  main {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    height: 80vh;
  }
  label {
    flex: 1;
    max-height: 48%;
    display: flex;
    flex-direction: column;
  }
  textarea {
    flex: 1;
    resize: none;
  }

  @media (min-width: 480px) {
    main {
      flex-direction: row;
    }
    label {
      max-height: unset;
      max-width: 48%;
    }
  }
</style>

<svelte:head>
  <title>Terser Online</title>
  <script src="https://unpkg.com/terser@4">

  </script>
</svelte:head>

<main>
  <label>
    <span>Input</span>
    <textarea bind:value="{code}"></textarea>
  </label>
  <label>
    <span>Output</span>
    <textarea disabled bind:value="{minifiedCode}"></textarea>
  </label>
</main>

<button on:click="{onCopy}">Copy</button>
