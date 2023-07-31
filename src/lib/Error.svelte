<script lang="ts">
  import { getContext } from "svelte";
  const navigate: any = getContext("navigate");
  const back = () => {
    navigate("register");
  };
</script>

<svelte:head>
  <title>Erro no registro</title>
</svelte:head>

<main class="page">
  <div class="container d-flex flex-column center gap-3">
    <h1>404</h1>
    <h3>Opa! Algo errado…</h3>
    <p>
      Algum erro inesperado aconteceu, não foi possivel renderizar á pagina,
      verifique sua conexão de internet e tente novamente!
    </p>
    <button class="btn btn-primary" on:click={() => back()}>Voltar</button>
  </div>
</main>

<style>
  .page {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .center {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  h1 {
    font-size: 5em;
  }
</style>
