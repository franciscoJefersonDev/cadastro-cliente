<script lang="ts">
  import { setContext } from "svelte";
  import Register from "./lib/Register.svelte";
  import Confirmation from "./lib/Confirmation.svelte";
  import Success from "./lib/Success.svelte";
  import Error from "./lib/Error.svelte";
  let application_state = "register";
  let data = {
    tipo: "Pessoa física",
    nome: "",
    cpf_cnpj: null,
    rg: null,
    sexo: "Masculino",
    nacionalidade: "",
    nome_fantasia: "",
    filiacao_01: "",
    filiacao_02: "",
    telefone: "",
    email: "",
    cep: null,
    tipo_de_local: "Zona urbana",
    estado: "",
    cidade: "",
    bairro: "",
    rua: "",
    numero: null,
    distrito: "",
    localidade_fazenda_sitio: "",
    complemento: "",
  };
  const navigate = async (to) => {
    application_state = to;
  };
  const reset_value = () => {
    data = {
      tipo: "Pessoa física",
      nome: "",
      cpf_cnpj: null,
      rg: null,
      sexo: "Masculino",
      nacionalidade: "",
      nome_fantasia: "",
      filiacao_01: "",
      filiacao_02: "",
      telefone: "",
      email: "",
      cep: null,
      tipo_de_local: "Zona urbana",
      estado: "",
      cidade: "",
      bairro: "",
      rua: "",
      numero: null,
      distrito: "",
      localidade_fazenda_sitio: "",
      complemento: "",
    };
  };

  setContext("navigate", navigate);
  setContext("reset_value", reset_value);
</script>

<main class="page">
  {#if application_state === "register"}
    <Register {data} />
  {:else if application_state === "confirmation"}
    <Confirmation {data} />
  {:else if application_state === "success"}
    <Success />
  {:else}
    <Error />
  {/if}
</main>

<style>
</style>
