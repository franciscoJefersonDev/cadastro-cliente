<script lang="ts">
  import { getContext } from "svelte";
  export let data;
  const navigate: any = getContext("navigate");
  const reset_value: any = getContext("reset_value");
  const back = () => {
    reset_value();
    navigate("register");
  };
</script>

<svelte:head>
  <title>Sucesso no registro</title>
</svelte:head>

<main class="page">
  <div class="container d-flex flex-column center gap-3">
    <h3>Sua requisição foi enviada para análise de nossos atendentes</h3>
    <h5>Você já pode fechar essa página, e aguardar o atendimento.</h5>
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
