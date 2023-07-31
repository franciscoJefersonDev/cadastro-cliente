<script lang="ts">
  import { getContext } from "svelte";
  import { onMount } from "svelte";
  import axios from "axios";
  export let data;
  const navigate: any = getContext("navigate");

  const on_change_type_person = (selected_type_person: string) => {
    data.tipo = selected_type_person;
  };
  const on_change_sex_person = (selected_sex_person: string) => {
    data.sexo = selected_sex_person;
  };
  const verify_cep = async () => {
    if (!data.cep || data.cep === "") return;
    axios
      .get(`https://viacep.com.br/ws/${data.cep}/json/`)
      .then(async (response) => {
        if (response.status !== 200) return;
        if (response.data.erro) return;
        data.estado = response.data.uf;
        await get_cities_from_state();
        data.bairro = response.data.bairro;
        data.rua = response.data.logradouro;
        data.cidade = response.data.localidade;
        document.querySelector("#states ").value = response.data.uf;
        document.querySelector("#city").value = response.data.localidade;
      });
    console.log(data);
  };
  const get_states = () => {
    axios
      .get(
        "https://servicodados.ibge.gov.br/api/v1/localidades/estados?orderBy=nome"
      )
      .then((response) => {
        response.data.forEach((item) => {
          const option = document.createElement("option");
          option.value = item.sigla;
          option.textContent = `${item.sigla} - ${item.nome}`;
          document.querySelector("#states").append(option);
        });
      });
  };
  const get_cities_from_state = async () => {
    await axios
      .get(
        `https://servicodados.ibge.gov.br/api/v1/localidades/estados/${data.estado}/municipios`
      )
      .then((response) => {
        response.data.forEach((item, index) => {
          const select_element = document.querySelector("#city");
          index === 0 ? (select_element.innerHtml = "") : null;
          const option = document.createElement("option");
          option.value = item.nome;
          option.textContent = item.nome;
          select_element.append(option);
        });
      });
  };
  const get_district_from_citie = async () => {
    await axios
      .get(
        ` https://servicodados.ibge.gov.br/api/v1/localidades/municipios/${data.cidade}`
      )
      .then(async (response) => {
        if (!response) return;
        await axios
          .get(
            `https://servicodados.ibge.gov.br/api/v1/localidades/municipios/${response.data.id}/distritos`
          )
          .then((response) => {
            response.data.forEach((item, index) => {
              const select_element = document.querySelector("#district");
              if (!select_element) return;
              index === 0 ? (select_element.innerHtml = "") : null;
              const option = document.createElement("option");
              option.value = item.nome;
              option.textContent = item.nome;
              select_element.append(option);
            });
          });
      });
  };
  const on_submit_data = (event) => {
    if (!event.target.checkValidity()) {
      event.preventDefault();
      event.stopPropagation();
    } else {
      navigate("confirmation");
    }
    event.target.classList.add("was-validated");
  };
  onMount(async () => {
    await get_cities_from_state();
    if (data.tipo === "Pessoa física") {
      document.querySelector("#pessoa-fisica").checked = true;
      if (data.sexo === "Masculino") {
        document.querySelector("#masculino").checked = true;
      } else {
        document.querySelector("#feminino").checked = true;
      }
    } else {
      document.querySelector("#pessoa-juridica").checked = true;
    }
    document.querySelector("#states").value = data.estado;
    document.querySelector("#city").value = data.cidade;
  });
  get_states();
</script>

<main class="register-component">
  <div class="container p-2">
    <header
      class="bg-danger bg-gradient rounded d-flex align-items-center justify-content-center p-2"
    >
      <div
        class="row p-3 rounded d-flex align-items-center justify-content-center text-center"
      >
        <h5 class="text-center text-light">
          Cartório Moura. Oﬁcial do Registro de Imóveis da Comarca de
          Itapipoca/CE
        </h5>
      </div>
    </header>

    <hr />

    <p>Nesta área, disponibilizamos algumas minutas para um pré-cadastro</p>
    <form
      class="row g-3 needs-validation"
      novalidate
      on:submit|preventDefault={(event) => on_submit_data(event)}
    >
      <div class="col-md-12">
        <h5>Dados pessoais</h5>
      </div>
      <div class="row">
        <h6>Tipo</h6>
        <label class="form-label" for="pessoa-fisica">
          <input
            type="radio"
            name="tipo"
            id="pessoa-fisica"
            value="Pessoa física"
            on:change={({ target }) => on_change_type_person(target.value)}
          />
          Pessoa física
        </label>
        <label class="form-label" for="pessoa-juridica">
          <input
            type="radio"
            name="tipo"
            id="pessoa-juridica"
            value="Pessoa jurídica"
            on:change={({ target }) => on_change_type_person(target.value)}
          />
          Pessoa jurídica
        </label>
      </div>
      <div class="col-md-12">
        <label for="nome" class="form-label"
          >Nome <span class="text-danger">*</span></label
        >
        <input
          type="text"
          class="form-control"
          id="nome"
          placeholder="Jose, Ana..."
          bind:value={data.nome}
          required
        />
      </div>

      <div class="col-md-12">
        <label for="cpf" class="form-label"
          >CPF/CNPJ <span class="text-danger">*</span></label
        >
        <input
          type="number"
          class="form-control"
          id="cpf-cnpj"
          bind:value={data.cpf_cnpj}
          required
        />
      </div>
      {#if data.tipo === "Pessoa física"}
        <div class="row pessoa-fisica">
          <div class="col-md-6">
            <label for="rg" class="form-label">RG (Registro Geral)</label>
            <input type="number" class="form-control" bind:value={data.rg} />
          </div>
          <div class="col-md-6">
            <div class="row">
              <span>Sexo</span>
              <label class="form-label" for="masculino">
                <input
                  type="radio"
                  name="sexo"
                  id="masculino"
                  value="Masculino"
                  checked
                  on:change={({ target }) => on_change_sex_person(target.value)}
                />
                Masculino
              </label>
              <label class="form-label" for="feminino">
                <input
                  type="radio"
                  name="sexo"
                  id="feminino"
                  value="Feminino"
                  on:change={({ target }) => on_change_sex_person(target.value)}
                />
                Feminino
              </label>
            </div>
          </div>
          <div class="col-md-6">
            <label for="nacionalidade" class="form-label">Nacionalidade</label>
            <input
              type="text"
              class="form-control"
              bind:value={data.nacionalidade}
            />
          </div>
        </div>
      {:else}
        <div class="col-md-12">
          <label for="nire" class="form-label">Nome fantasia</label>
          <input
            type="text"
            class="form-control"
            bind:value={data.nome_fantasia}
          />
        </div>
      {/if}
      <div class="col-md-12">
        <label for="fili-01" class="form-label">Filiação 01</label>
        <input
          type="text"
          class="form-control"
          id="fili-01"
          placeholder="Filiação..."
          bind:value={data.filiacao_01}
        />
      </div>
      <div class="col-md-12">
        <label for="fili-02" class="form-label">Filiação 02</label>
        <input
          type="text"
          class="form-control"
          id="fili-02"
          placeholder="Filiação..."
          bind:value={data.filiacao_02}
        />
      </div>
      <div class="col-md-12">
        <h5>Telefone/Contato</h5>
      </div>
      <div class="row">
        <div class="col-md-6">
          <label for="tel" class="form-label">
            Telefone
            <span class="text-danger">*</span>
          </label>
          <input
            type="text"
            class="form-control"
            placeholder="(00) 00000-0000"
            required
            bind:value={data.telefone}
          />
        </div>
        <div class="col-md-6">
          <label for="email" class="form-label">Email</label>
          <input type="email" class="form-control" bind:value={data.email} />
        </div>
      </div>
      <div class="col-md-12">
        <h5>Endereço</h5>
      </div>
      <div class="row">
        <div class="col-md-6">
          <label for="cep" class="form-label">CEP</label>
          <div class="input-group">
            <input type="text" class="form-control" bind:value={data.cep} />
            <button
              class="btn btn-danger"
              type="button"
              on:click={() => verify_cep()}>Verificar</button
            >
          </div>
        </div>
      </div>
      <div class="col-md-6">
        <label class="form-label">
          Tipo de local
          <span class="text-danger">*</span>
        </label>
        <select
          class="form-control select"
          on:change={({ target }) => {
            data.tipo_de_local = target.value;
            get_district_from_citie();
          }}
        >
          <option value="Zona urbana" label="Zona urbana" />
          <option value="Zona rural" label="Zona rural" />
        </select>
      </div>
      <div class="row">
        <div class="col-md-6">
          <label class="form-label"
            >Estado <span class="text-danger">*</span></label
          >
          <select
            class="form-control select"
            name="states"
            id="states"
            on:change={({ target }) => {
              data.estado = target.value;
              get_cities_from_state();
            }}
            required
          >
            <option value="" label=" " />
          </select>
        </div>
        <div class="col-md-6">
          <label class="form-label">
            Cidade
            <span class="text-danger">*</span>
          </label>
          <select
            class="form-control select"
            name="city"
            id="city"
            on:change={({ target }) => {
              data.cidade = target.value;
              get_district_from_citie();
            }}
            required
          >
            <option value="" label=" " />
          </select>
        </div>
      </div>
      {#if data.tipo_de_local === "Zona urbana"}
        <div class="row">
          <div class="col-md-6">
            <label for="neighborhood" class="form-label">Bairro</label>
            <input
              type="text"
              class="form-control"
              id="neighborhood"
              bind:value={data.bairro}
            />
          </div>
          <div class="col-md-6">
            <label for="street" class="form-label">Rua</label>
            <input
              type="text"
              class="form-control"
              id="street"
              bind:value={data.rua}
            />
          </div>
          <div class="col-md-6">
            <label for="number" class="form-label">Numero</label>
            <input
              type="number"
              class="form-control"
              id="number"
              bind:value={data.numero}
            />
          </div>
        </div>
      {:else}
        <div class="col-md-6">
          <label class="form-label">
            Distrito
            <span class="text-danger">*</span>
          </label>
          <select
            class="form-control select"
            name="district"
            id="district"
            on:change={({ target }) => (data.distrito = target.value)}
            required
          >
            <option value="" label=" " />
          </select>
        </div>
        <div class="col-md-6">
          <label for="rural-local" class="form-label"
            >Localidade/Fazenda/Sítio</label
          >
          <input
            type="text"
            class="form-control"
            id="rural-local"
            bind:value={data.localidade_fazenda_sitio}
          />
        </div>
      {/if}
      <div class="col-md-12">
        <label for="complement" class="form-label">Complemento</label>
        <input
          type="text"
          class="form-control"
          id="complement"
          placeholder="Complemento..."
          bind:value={data.complemento}
        />
      </div>
      <div class="col-12 d-flex justify-content-start">
        <button
          class="btn btn-danger bg-gradient d-flex flex-row justify-content-center align-items-center gap-3"
          type="submit"
        >
          <div class="spinner-border d-none" role="status">
            <span class="visually-hidden">Loading...</span>
          </div>
          Continuar
        </button>
      </div>
    </form>
  </div>
</main>

<style>
  header {
    width: 100%;
    height: 10em;
  }
</style>
