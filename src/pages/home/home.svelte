<script>
    import { onMount } from 'svelte';
  import {push} from 'svelte-spa-router';
    import AddCard from '../../componants/addCard.svelte';
    import AddList from '../../componants/addList.svelte';
    import { API_URL } from '../../constantes';

  ////////////////////// VERIF DE CONNEXION
  let tokenValue = localStorage.getItem('token');
  if(!tokenValue) {
    push('/login');
  }
  /////////////////////////////////////////

  const updateCard = async (cardId, listId) => {
    const result = await fetch(API_URL + "card/" + cardId, {
      method: "PUT",
      headers: {
        "Content-Type": "application/json", // les types de donées que je t'envoi c'est du json
        authorization: "Bearer " + localStorage.getItem("token"),
      },
      body: JSON.stringify({
        list_id: listId
      }),
    });

    reload();
  }

  ////////////////////  DRAG AND DROP
	let draggingCardId;

	const dragStart = (event, cardId) => {
		draggingCardId = cardId;
	}

	const drop = (event, listId) => {
		console.log(`Carte avec ID : ${draggingCardId} laché sur ${listId}`);
		const card = cards.find(c => c.id === draggingCardId); // retourner la première carte qui répond au critère
		card.list_id = listId;

		cards = cards; // JE viens déclancher le fait qu'il "a changé"

    updateCard(draggingCardId, listId);
		draggingCardId = null;
	}

  ///////////////////////////////////////////


  /////////////// CHARGEMENT DE LA PAGE ET DES DONNNES

	let lists = [
		{ id: 1, name: "A Faire"},
		{ id: 2, name: "En cours"},
		{ id: 3, name: "Fait "}
	];

	let cards = [
		// { id: 1, list_id: 1, title: "Commit mon code", tags: [{ id: 1, name: 'URGENT'}]},
		// { id: 2, list_id: 2, title: "Etape 3 de l'atelier", tags: [{ id: 2, name: 'PRIORITAIRE'}]},
		// { id: 3, list_id: 3, title: "Etape 2 de l'atelier", tags: []},
		// { id: 4, list_id: 1, title: "Acheter des chips", tags: [{ id: 3, name: 'FACULTATIF'}]},
		// { id: 5, list_id: 2, title: "Voler le chat de la voisine", tags: [{ id: 1, name: 'URGENT'}]},
		// { id: 6, list_id: 3, title: "Kidnapper son chihuahua", tags: [{ id: 2, name: 'PRIORITAIRE'}]},
		// // ... Rajoutez en avec le list_id 2 et 3 ainsi qu'un ou deux tags de plus 
	];
  const getLists = async () => {
    const res = await fetch(API_URL + 'list', {
      headers: {
        authorization: 'Bearer ' + localStorage.getItem('token')
      }
    });

    return await res.json();
  }

  const getCards = async () => {
    const res = await fetch(API_URL + 'card?include=tags', {
      headers: {
        authorization: 'Bearer ' + localStorage.getItem('token')
      }
    });

    return await res.json();
  }

  onMount(async () => {
    await load();
    console.log(cards);
  });

  const load = async () => {
    lists = await getLists();
    cards = await getCards();
  }


  const deleteList = async (id) => {
    if(confirm("Êtes-vous sur de vouloir supprimer cette liste et toutes ses cartes ? ")) {
      const result = await fetch(API_URL + 'list/' + id, {
      method: "DELETE",
      headers: {
        authorization: 'Bearer ' + localStorage.getItem('token')
      }
    });

    load();
    }
  }

  const deleteCard = async (id) => {
    if(confirm("Êtes-vous sur de vouloir supprimer cette carte? ")) {
      const result = await fetch(API_URL + 'card/' + id, {
      method: "DELETE",
      headers: {
        authorization: 'Bearer ' + localStorage.getItem('token')
      }
    });

    load();
    }
  }



</script>

<main>
	<header class="d-flex flex-wrap justify-content-center py-3 mb-4 border-bottom">
	<a href="/" class="d-flex align-items-center mb-3 mb-md-0 me-md-auto text-dark text-decoration-none">
		<svg class="bi me-2" width="40" height="32"><use xlink:href="#bootstrap"></use></svg>
		<span class="fs-4">oKanban</span>
	</a>
</header>
	<div class="container h-full">
		<div class="row h-full">
			{#each lists as list}
			<div class="col-sm h-full">
				<div class="list"
					on:drop={event => drop(event, list.id)}
					ondragover="return false"
				>
					<h2>{list.name} <button on:click={() => deleteList(list.id)} class="btn btn-danger btn-sm">X</button></h2>
					{#each cards as card}
						{#if card.list_id === list.id}
							<div class="card"
								draggable={true}
								on:dragstart={event => dragStart(event, card.id)}
							>
								<h3>{card.title}</h3>
                <button on:click={() => deleteCard(card.id)} class="btn btn-outline-danger btn-sm">X</button>
								<div class="tags">
									{#each card.tags as tag}
										<div class="tag">
											{tag.name}
										</div>
									{/each}
								</div>
							</div>
						{/if}
					{/each}
          <AddCard reload={load} listId={list.id} />
				</div>
			</div>
			{/each}
      <AddList reload={load} />
		</div>
	</div>
</main>

<style>

	.list {
		background-color: #CCC;
		border-radius: 15px;
		padding: 5px;
		height: 100%;
		margin-bottom: 30px;
	}

	.list > h2 {
		font-size: 20px;
	}

	.card {
		padding: 5px;
		margin-bottom: 5px;
		cursor: grab;

	}

	.card > h3 {
		font-size: 16px
	}

	.tags {
		display: flex;
	}

	.tag {
		background-color: #EEE;
		padding: 5px;
		border-radius: 10px;
		text-transform: uppercase;
		margin-right: 5px;
	}
</style>