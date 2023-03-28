<template>
  <div class="dog-card">
    <!-- <img src="https://via.placeholder.com/150" alt="Dog image"> -->
    <h2>{{ dog.name }}</h2>
    <div class="card-details">
      <p>
        {{ dog.gender === 'M' ? 'Macho' : 'Fêmea' }} | {{ dog.weight }}kg {{ dog.height }}cm 
      </p>
      <p v-if="dog.mother && dog.father">
        Mãe: {{ dog.mother?.name }} Pai: {{ dog.father?.name }}
      </p>
      <p>Raças: {{ dog.breed }}</p>
      <p v-for="breed in dog.breeds" :key="breed.id" :value="breed">- {{ breed.breed.name }} {{ breed.percent }}%</p>
    </div>
  </div>
</template>

<script>
export default {
  props: ['dog']
}
</script>

<style lang="scss">
  .dog-card {
    width: 100%;
    height: 100%;
    padding: 1rem;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
    border: 1px solid #ccc;
    border-radius: 5px;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    img {
      width: 100%;
      height: auto;
    }
  }
</style>
