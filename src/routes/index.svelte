<script>
  import supabase from "$lib/db";
  let data = "",
    add = false,
    title = "Enter Title Here",
    code = "Enter Code Here ";
  function getData() {
    return supabase.from("Ameen").select(`*`);
  }
  async function addData(title, code) {
    await supabase.from("Ameen").insert({
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
</script>

<body>
  <main>
    {#await getData()}
      heyy
    {:then item}
      {#each item.data as d}
        <div class="item">
          <section class="heading">
            {d.title}
            <button on:click={() => fallbackCopyTextToClipboard(d.code)}
              >Copy Code</button
            >
          </section>
        </div>

        <!-- <button on:click={addData}>vvv</button>
<pre><textarea bind:value={data}/></pre>
-->
      {/each}
    {/await}

    {#if add == true}
      <div id="adding">
        <form class="box">
          <h1>Add</h1>
          <input type="Title" name="" placeholder="Title" bind:value={title} />
          <textarea name="code" bind:value={code} />
          <button on:click={() => addData(title, code)}>Add</button>
        </form>
      </div>
    {:else}
      <button on:click={() => (add = true)}>Add</button>
    {/if}
  </main>
</body>

<style>
  .item {
    width: 100vw;
    height: fit-content;
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
  }
  .heading {
    padding-top: 5px;
    padding-bottom: 5px;
    font-family: "Lucida Sans", "Lucida Sans Regular", "Lucida Grande",
      "Lucida Sans Unicode", Geneva, Verdana, sans-serif;
    font-size: large;
    color: #222222;
  }

  body {
    margin: 0;
    padding: 0;
    font-family: sans-serif;
    background-color: #66ccff;
  }
  textarea {
    background: none;
    border: 2px solid rgb(28, 216, 230);
    color: black;
    width: 281px;
    height: 108px;
  }
  .box {
    width: 300px;
    padding: 40px;
    position: absolute;
    top: 50%;
    left: 50%;
    border-radius: 24px;
    transform: translate(-50%, -50%);
    background: white;
    text-align: center;
  }
  .box {
    color: blue;
    text-transform: upppercase;
    font-weight: 500;
  }
</style>
