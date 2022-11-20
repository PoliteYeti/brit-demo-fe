<script>
  import { onMount } from "svelte";
  import Datarow from "./Datarow.svelte";

  let data = [];
  let new_price = 0;
  let new_description = "New item description";
  let inputMode = true;
  let summaryCost = 0;

  async function getData() {
    const response = await fetch(
      `https://wps536osra.execute-api.us-east-2.amazonaws.com/dev/api/items`
    );
    data = await response.json();
  }

  async function handleDelete(id) {
    const response = await fetch(
      `https://wps536osra.execute-api.us-east-2.amazonaws.com/dev/api/items/${id}`,
      {
        method: "DELETE",
        headers: {
          accept: "application/json",
        },
      }
    );
    if (response.ok) {
      console.log("Item deleted");
    }
    getData();
  }

  async function addItem() {
    const response = await fetch(
      "https://wps536osra.execute-api.us-east-2.amazonaws.com/dev/api/items",
      {
        method: "POST",
        headers: {
          accept: "application/json",
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          price: new_price,
          description: new_description,
        }),
      }
    );

    if (response.ok) {
      console.log("Item added");
    }

    getData();
    new_price = 0;
    new_description = "New item description";
  }

  async function toggleMode() {
    if (inputMode) {
      const response = await fetch(
        "https://wps536osra.execute-api.us-east-2.amazonaws.com/dev/api/summary",
        {
          headers: {
            accept: "application/json",
          },
        }
      );
      let summaryData = await response.json();
      summaryCost = summaryData.totalcost;
    }

    inputMode = !inputMode;
  }

  onMount(() => {
    getData();
  });
</script>

<div class="bluebox">
  <div class="tablecontainer">
    <table>
      {#if inputMode}
        <tr>
          <th>Description</th>
          <th>Price</th>
        </tr>
        {#each data as item}
          <Datarow
            id={item.id}
            description={item.description}
            price={item.price}
            on:delete={handleDelete(item.id)}
          />
        {/each}
        <tfoot>
          <td><input bind:value={new_description} /></td>
          <td><input bind:value={new_price} /></td>
          <td><button on:click={addItem}>add</button></td>
        </tfoot>
      {:else}
        <tr>
          <td>Total cost</td>
          <td>{summaryCost}</td>
        </tr>
      {/if}
    </table>
  </div>
  <button id="summary" on:click={toggleMode}>Toggle</button>
</div>

<style>
  table {
    border-collapse: collapse;
    border: 1px solid black;
    margin: 10px;
    width: 100%;
    background-color: white;
  }

  td {
    border: 1px solid grey;
    padding: 0px;
  }

  input {
    display: table-cell;
    width: auto;
    border: none;
    margin: none;
  }

  tfoot {
    background-color: lightgray;
  }

  button {
    width: auto;
  }

  input {
    padding: 0px;
    margin: 0px;
  }

  #summary {
    width: 20vw;
    margin: 10px;
  }

  .bluebox {
    width: 70vw;
    margin: auto;
    background-color: blue;
    border-radius: 10px;
    text-align: center;
  }

  .tablecontainer {
    margin: auto;
    padding: 10px;
    min-height: 50vh;
    width: 60vw;
  }
</style>
