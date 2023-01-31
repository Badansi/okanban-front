<script>
  import { API_URL } from "../constantes";

  export let reload;
  export let listId;

  let isAddCard = false;

  let cardTitle;

  const addCard = async () => {
    const result = await fetch(API_URL + "card", {
      method: "POST",
      headers: {
        "Content-Type": "application/json", // les types de donées que je t'envoi c'est du json
        authorization: "Bearer " + localStorage.getItem("token"),
      },
      body: JSON.stringify({
        title: cardTitle,
        list_id: listId
      }),
    });

    isAddCard = false;
    reload();
  };
</script>

<div class="full-width">
  {#if isAddCard === false}
  <button class="full-width btn btn-primary" on:click={() => isAddCard = true}>+</button>
  {:else}
    <form class="card p-5" on:submit|preventDefault={addCard} >
      <div class="form-outline mb-4">
        <input bind:value={cardTitle} type="text" id="form2Example1" name="name" class="form-control" />
        <label class="form-label" for="form2Example1">Card Title</label>
      </div>
      
      <button type="submit" class="btn btn-primary btn-block mb-4">Create</button>
    </form>
  {/if}
</div>

<style>
  .full-width {
    width: 100%;
  }
</style>
