<template>
  <div class="about">
    <h1>Vocabulos</h1>
    <p><label for="termo">Termo: </label><input id="termo" type="text" v-model="vocabulo.termo"/></p>
    <p><label for="significado">Significado: </label><input id="significado" type="text" v-model="vocabulo.significado"/></p>
    <p><label for="versao">Versão: </label><input id="versao" type="number" v-model="vocabulo.versao"/></p>
    <p><label for="dataHoraDesativacao">Data/Hora Desativação: </label><input id="dataHoraDesativacao" type="datetime-local" v-model="vocabulo.dataHoraDesativacao"/></p>
    <p><label for="dataHoraCadastro">Data/Hora Cadastro: </label><input id="dataHoraCadastro" type="datetime-local" v-model="vocabulo.dataHoraCadastro"/></p>
    <button>Atualizar</button>
    <button @click="incluir">Incluir</button>

    <table>
      <thead>
        <td>Id</td>
        <td>Termo</td>
        <td>Significado</td>
        <td>Versão</td>
      </thead>
      <tbody>
        <tr v-for="vocabulo in vocabulos" :key="vocabulo.id">
          <td>{{ vocabulo.id }}</td>
          <td>{{ vocabulo.termo }}</td>
          <td>{{ vocabulo.significado }}</td>
          <td>{{ vocabulo.versao }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>


<script setup lang="ts">
import { onMounted, ref } from 'vue';
import axios from 'axios';

const vocabulos = ref();
const vocabulo = ref({
  termo: '',
  significado: '',
  versao: null,
  dataHoraCadastro: '',
  dataHoraDesativacao: ''
});
async function buscarVocabulos() {
  vocabulos.value = (await axios.get("vocabulo")).data;
  }

async function incluir() {
  try {
    const response = await axios.post("/vocabulo", vocabulo.value);
    console.log('Vocabulo criado com sucesso:', response.data);
  } catch (error) {
    console.error('Erro ao criar Vocabulo:', error);
  }
}

onMounted(() => {
  buscarVocabulos()
})
</script>
