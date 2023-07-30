<script lang="ts">
  import { getContext } from "svelte";
  import axios from "axios";
  export let data;
  const navigate: any = getContext("navigate");
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

    <p>Verifique os seus dados antes de enviar o cadastro</p>

    <div class="col-md-12">
      <h5>Dados pessoais</h5>
    </div>
    <div class="row">
      <h6>Tipo</h6>
      <label class="form-label">
        <input type="radio" name="tipo" checked />
        {data.tipo}
      </label>
    </div>
    <div class="col-md-12">
      <label for="nome" class="form-label">Nome</label>
      <input
        type="text"
        class="form-control"
        id="nome"
        placeholder="Jose, Ana..."
        bind:value={data.nome}
        readonly
      />
    </div>

    <div class="col-md-12">
      <label for="cpf" class="form-label">CPF/CNPJ</label>
      <input
        type="number"
        class="form-control"
        id="cpf-cnpj"
        bind:value={data.cpf_cnpj}
        readonly
      />
    </div>
    {#if data.tipo === "Pessoa física"}
      <div class="row pessoa-fisica">
        <div class="col-md-6">
          <label for="rg" class="form-label">RG (Registro Geral)</label>
          <input
            type="number"
            class="form-control"
            bind:value={data.rg}
            readonly
          />
        </div>
        <div class="col-md-12">
          <div class="row">
            <h6>Sexo</h6>
            <label class="form-label">
              <input type="radio" name="sexo" checked />
              {data.sexo}
            </label>
          </div>
        </div>
        <div class="col-md-6">
          <label for="nacionalidade" class="form-label">Nacionalidade</label>
          <input
            type="text"
            class="form-control"
            bind:value={data.nacionalidade}
            readonly
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
          readonly
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
        readonly
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
        readonly
      />
    </div>
    <div class="col-md-12">
      <h5>Telefone/Contato</h5>
    </div>
    <div class="row">
      <div class="col-md-6">
        <label for="tel" class="form-label"> Telefone </label>
        <input
          type="text"
          class="form-control"
          placeholder="(00) 00000-0000"
          required
          bind:value={data.telefone}
          readonly
        />
      </div>
      <div class="col-md-6">
        <label for="email" class="form-label">Email</label>
        <input
          type="email"
          class="form-control"
          bind:value={data.email}
          readonly
        />
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
    <div class="col-12 d-flex justify-content-center">
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
  </div>
</main>

<style>
  header {
    width: 100%;
    height: 10em;
  }
</style>
