<script>
    import { API_URL } from "../constantes";

  export let reload;

  let isAddList = false;

  let listName;

  const addList = async () => {
    const result = await fetch(API_URL + 'list', {
      method: "POST",
      headers: {
        'Content-Type': 'application/json', // les types de donées que je t'envoi c'est du json
        authorization: 'Bearer ' + localStorage.getItem('token')
      },
      body: JSON.stringify({
        name: listName
      })
    });

    isAddList = false;
    reload();
  }
</script>

<div class="col-sm h-full">
  <div class="list h-full flex-add">
    {#if isAddList == false}
      <button type="button" class="btn btn-primary" on:click={() => isAddList = true}>+</button>
    {:else}
      <form on:submit|preventDefault={addList} class="m-4">
        <div class="form-outline mb-4">
          <input bind:value={listName} type="text" id="form2Example1" name="name" class="form-control" />
          <label class="form-label" for="form2Example1">List name</label>
        </div>
        
        <button type="submit" class="btn btn-primary btn-block mb-4">Create</button>
      </form>
    {/if}
  </div>
</div>

<style>
  .flex-add {
    background-color: #11cccc;
    border-radius: 15px;
    display: flex;
    font-size: 20px;
    align-items: center;
    justify-content: center;
    font-weight: bold;
  }
</style>
