<script>
  import { createEventDispatcher } from "svelte";

  export let id;
  export let description;
  export let price;

  async function handleChange() {
    const response = await fetch(
      `https://wps536osra.execute-api.us-east-2.amazonaws.com/dev/api/items/${id}`,
      {
        method: "PUT",
        headers: {
          accept: "application/json",
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          price: price,
          description: description,
        }),
      }
    );
    if (response.ok) {
      console.log("Item updated");
    }
  }

  const dispatch = createEventDispatcher();

  function handleClick() {
    dispatch("delete");
  }
</script>

<tr>
  <td><input bind:value={description} on:change={handleChange} /></td>
  <td><input bind:value={price} on:change={handleChange} /></td>
  <td><button on:click={handleClick}>delete</button></td>
</tr>

<style>
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

  button {
    width: auto;
    background-color: salmon;
  }
</style>
