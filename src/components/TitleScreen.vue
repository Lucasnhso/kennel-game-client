<template>
  <header>
    <h1>Seus Cachorros</h1>
    <button @click="showModal = true">Cruzamento</button>
  </header>
    

  <div class="card-list">
    <DogCard :dog="dog"  v-for="dog in dogs" :key="dog.id"/>
  </div>
  <CruzamentoModal :showModal="showModal" @close="showModal = false" :dogs="dogs" />
</template>

<script>
import DogCard from './DogCard.vue'
import CruzamentoModal from './CruzamentoModal.vue'
import axios from 'axios';

export default {
  components: {
    DogCard,
    CruzamentoModal
  },
  data() {
    return {
      dogs: [],
      showModal: false
    }
  },
  created() {
    axios.get('http://localhost:5000/dogs')
      .then(response => {
        this.dogs = response.data;
      })
      .catch(error => {
        console.error(error);
      });
  }
}
</script>
<style lang="scss">
  .card-list {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(276px,1fr));
    gap: 1.6rem;
    padding: 1.6rem;
  }
  header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 1.6rem
  }
</style>