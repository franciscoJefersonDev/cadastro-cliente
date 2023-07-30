<script lang="ts">
  import { setContext } from "svelte";
  import Register from "./lib/Register.svelte";
  import Comfirmation from "./lib/Comfirmation.svelte";
  let application_state = "confirmation";
  let data = {
    tipo: "Pessoa física",
    nome: "Jose",
    cpf_cnpj: 99999,
    rg: 809898,
    sexo: "Masculino",
    nacionalidade: "wqdwqedew",
    nome_fantasia: "dedede",
    filiacao_01: "eded",
    filiacao_02: "dedede",
    telefone: "frfgrfgrfr",
    email: "frfrf",
    cep: "",
    tipo_de_local: "Zona urbana",
    estado: "",
    cidade: "",
    bairro: "",
    rua: "",
    numero: "",
    distrito: "",
    localidade_fazenda_sitio: "",
    complemento: "",
  };
  const navigate = async (to) => {
    application_state = to;
  };

  setContext("navigate", navigate);
</script>

<main class="page">
  {#if application_state === "register"}
    <Register {data} />
  {:else}
    <Comfirmation {data} />
  {/if}
</main>

<style>
</style>
