<script>
  let code = "";
  let isEncoding = true;
  let encoded = "";
  $: {
    try {
      encoded = (isEncoding ? encodeURIComponent : decodeURIComponent)(code);
    } catch (e) {}
  }
  $: sourceText = isEncoding ? "Encode" : "Decode";

  function onCopy() {
    navigator.permissions
      .query({ name: "clipboard-write" })
      .then(({ state }) => {
        if (state !== "denied") throw Error("Can't write to clipboard");
      })
      .then(() => {
        navigator.clipboard.writeText(encoded).then(() => {
          console.log("Copied to clipboard");
        });
      })
      .catch(err => {
        console.error(err);
        if (err.message) alert(err.message);
      });
  }
  function onSwitch() {
    isEncoding = !isEncoding;
  }
</script>

<style>
  article {
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
    article {
      flex-direction: row;
    }
    label {
      max-height: unset;
      max-width: 48%;
    }
  }
</style>

<svelte:head>
  <title>String encode/decode</title>
</svelte:head>

<main>
  <h2>{sourceText}</h2>
  <button on:click="{onSwitch}">Switch</button>
  <article>
    <label>
      <span>Input</span>
      <textarea bind:value="{code}"></textarea>
    </label>
    <label>
      <span>Output</span>
      <textarea bind:value="{encoded}"></textarea>
    </label>
  </article>
</main>

<button on:click="{onCopy}">Copy</button>
