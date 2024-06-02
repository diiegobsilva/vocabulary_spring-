<template>
  <div class="about">
    <h1>Vocábulos</h1>
    <p>
      <label for="termo">Termo: </label>
      <input id="termo" type="text" v-model="vocabulo.termo" />
    </p>
    <p>
      <label for="significado">Significado: </label>
      <input id="significado" type="text" v-model="vocabulo.significado" />
    </p>
    <p>
      <label for="versao">Versão: </label>
      <input id="versao" type="number" v-model="vocabulo.versao" />
    </p>
    <p>
      <label for="dataHoraDesativacao">Data/Hora Desativação: </label>
      <input id="dataHoraDesativacao" type="datetime-local" v-model="vocabulo.dataHoraDesativacao" />
    </p>

    <button @click="incluir">Incluir</button>
    <button @click="buscarPorTermoEVersao">Buscar</button>

    <div v-if="mensagem">{{ mensagem }}</div>
    <table v-else>
      <thead>
        <tr>
          <th>Id</th>
          <th>Termo</th>
          <th>Significado</th>
          <th>Versão</th>
          <th>Situação</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="vocabulo in vocabulos" :key="vocabulo.id">
          <td>{{ vocabulo.id }}</td>
          <td>{{ vocabulo.termo }}</td>
          <td>{{ vocabulo.significado }}</td>
          <td>{{ vocabulo.versao }}</td>
          <td>{{ vocabulo.situacao }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>


<script setup lang="ts">
import { onMounted, ref } from 'vue';
import axios from 'axios';

const vocabulos = ref();
const mensagem = ref('');

const vocabulo = ref({
  termo: '',
  significado: '',
  versao: null,
  dataHoraDesativacao: '',
  dataHoraCadastro: ''
});

async function buscarVocabulos() {
  try {
    const response = await axios.get("/vocabulo");
    vocabulos.value = response.data.map((situacaoVocabulo: any) => ({
      ...situacaoVocabulo,
      situacao: situacaoVocabulo.dataHoraDesativacao ? 'desativado' : 'ativo',
    }));
    mensagem.value = '';
  } catch (error) {
    console.error('Erro ao buscar vocábulos:', error);
    mensagem.value = 'Erro ao buscar vocábulos';
  }
}

async function buscarPorTermoEVersao() {
  try {
    const response = await axios.get(`/vocabulo/${vocabulo.value.termo}/${vocabulo.value.versao}`);
    if (response.data.length > 0) {
      vocabulos.value = response.data.map((situacaoVocabulo: any) => ({
        ...situacaoVocabulo,
        situacao: situacaoVocabulo.dataHoraDesativacao ? 'desativado' : 'ativo',
      }));
      mensagem.value = '';
    } else {
      vocabulos.value = [];
      mensagem.value = 'Nenhum vocábulo foi encontrado para os critérios fornecidos';
    }
  } catch (error) {
    console.error('Erro ao buscar vocábulo por termo e versão:', error);
    mensagem.value = 'Erro ao buscar vocábulo por termo e versão';
  }
}

async function incluir() {
  try {
    const response = await axios.post("/vocabulo", vocabulo.value);
    console.log('Vocábulo criado com sucesso:', response.data);
    buscarVocabulos();
    vocabulo.value = {
      termo: '',
      significado: '',
      versao: null,
      dataHoraDesativacao: '',
      dataHoraCadastro: ''
    };
  } catch (error) {
    console.error('Erro ao criar Vocábulo:', error);
  }
}

onMounted(() => {
  buscarVocabulos();
});
</script>
