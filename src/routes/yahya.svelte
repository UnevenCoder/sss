<script>
  import supabase from "$lib/db";
  import { Stretch } from "svelte-loading-spinners";
  let data = "",
    add = false,
    title = "Enter Title Here",
    code = "Enter Code Here ",
    obj = {},
    edit = false,
    c = "",
    x = "current",
    num = Math.floor(Math.random() * 3);
  function getData() {
    return supabase.from("Yahya").select(`*`);
  }
  async function addData(title, code) {
    console.log("adding");
    await supabase.from("Yahya").insert({
      title: title,
      code: code,
    });
    console.log("pass");
    add = false;
  }
  function fallbackCopyTextToClipboard(text) {
    var textArea = document.createElement("textarea");
    textArea.value = text;

    // Avoid scrolling to bottom
    textArea.style.top = "0";
    textArea.style.left = "0";
    textArea.style.position = "fixed";

    document.body.appendChild(textArea);
    textArea.focus();
    textArea.select();

    try {
      var successful = document.execCommand("copy");
      var msg = successful ? "successful" : "unsuccessful";
      console.log("Fallback: Copying text command was " + msg);
    } catch (err) {
      console.error("Fallback: Oops, unable to copy", err);
    }

    document.body.removeChild(textArea);
  }
  function copyTextToClipboard(text) {
    if (!navigator.clipboard) {
      fallbackCopyTextToClipboard(text);
      return;
    }
    navigator.clipboard.writeText(text).then(
      function () {
        console.log("Async: Copying to clipboard was successful!");
      },
      function (err) {
        console.error("Async: Could not copy text: ", err);
      }
    );
  }

  async function delet(id) {
    const { data, error } = await supabase
      .from("Yahya")
      .delete()
      .match({ id: id });

    console.log(data, error);
  }

  async function update(code, id) {
    const { data, error } = await supabase
      .from("Yahya")
      .update({ code: code })
      .match({ id: id });

    console.log(data, error);
  }
</script>

<main>
  {#await getData()}
    <section id="loader">
      <Stretch size="60" color="#ffff00" unit="px" duration="0.5s" />
    </section>
  {:then item}
    {#each item.data as d, id}
      <span id="sss">{(obj[d.title] = [d.id, d.code])}</span>
      <div class="item">
        <section id={id == 0 ? "zero" : ""} class="heading">
          <h5>{d.title}</h5>
          <div class="buttons">
            <button on:click={() => fallbackCopyTextToClipboard(d.code)}
              >Copy Code</button
            >
            <button on:click={() => delet(obj[d.title][0])}>Delete</button>
          </div>
        </section>
      </div>

      <!-- <button on:click={addData}>vvv</button>
<pre><textarea bind:value={data}/></pre>
-->
    {/each}

    {#if add == true}
      <div id="adding">
        <div class="box">
          <h1>Add</h1>
          <input
            type="text"
            name=""
            placeholder="Enter title here"
            bind:value={title}
          />
          <textarea name="code" bind:value={code} />
          <button on:click={() => addData(title, code)}>Add</button>
          <button on:click={() => (add = false)}>Cancel</button>
        </div>
      </div>
    {:else}
      <button id="aaa" on:click={() => (add = true)}>Add</button>
    {/if}
  {/await}
  {#if edit == true}
    <span id="sss">{(c = obj[x][1])}</span>
    <textArea value={c} />
  {/if}
</main>

<style>
  .item {
    width: 75vw;
    height: fit-content;
    display: flex;
    flex-direction: column;
    margin-left: 70px;
  }
  .heading {
    padding-top: 5px;
    padding-bottom: 5px;
    font-family: "Lucida Sans", "Lucida Sans Regular", "Lucida Grande",
      "Lucida Sans Unicode", Geneva, Verdana, sans-serif;
    font-size: large;
    display: flex;
    flex-direction: column;
  }
  .heading button {
    margin-top: 10px;
  }
  .heading h5 {
    margin: 0;
    padding: 0;
  }
  h5:hover {
    color: whitesmoke;
  }
  button:hover {
    color: whitesmoke;
    border: 2px dotted crimson;
  }
  :global(body) {
    margin: 0;
    padding: 0;
    font-family: sans-serif;
    background-color: #222222;
    color: #ffff00;
    text-transform: capitalize;
  }
  textarea {
    background: none;
    border: 2px dotted yellow;
    width: 281px;
    height: 108px;
    color: white;
    margin-top: 5px;
  }

  textarea:hover {
    color: yellow;
    border: 2px dashed red;
  }

  .box input {
    background: none;
    border: 2px dotted yellow;

    color: white;
    text-align: center;
  }
  .box {
    width: 300px;
    padding: 40px;
    position: absolute;
    top: 50%;
    left: 50%;
    border-radius: 24px;
    transform: translate(-50%, -50%);
    text-align: center;
    background: none;
    color: yellow;
  }
  .box {
    text-transform: upppercase;
    font-weight: 500;
  }
  button {
    background-color: transparent;
    border: none;
    color: crimson;
    padding: 5px;
    border: 2px dotted green;
  }
  #aaa {
    margin-top: 50px;
    align-self: center;
    margin-left: 50vw;
    margin-right: 50vw;
    font-size: 20px;
    padding: 10px;
  }

  .buttons {
    width: 100%;
    margin-left: 50px;
  }
  #sss {
    display: none;
  }
  :global(#zero) {
    margin-top: 30px;
  }

  #loader {
    width: 100vw;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
