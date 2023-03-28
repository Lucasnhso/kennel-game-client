<template>
  <div class="modal" v-show="showModal">
    <div class="modal-content">
      <span class="close" @click="closeModal">&times;</span>
      <h2>Cruzamento</h2>
      <div class="inputs">
        <div class="input-group">
          <label for="father">Pai:</label>
          <select id="father" v-model="selectedFather">
            <option v-for="male in males" :value="male" :key="male.id">{{ male.name }}</option>
          </select>
        </div>
        <div class="input-group">
          <label for="mother">Mãe:</label>
          <select id="mother" v-model="selectedMother">
            <option v-for="female in females" :value="female" :key="female.id">{{ female.name }}</option>
          </select>
        </div>
      </div>
      <button @click="breed" v-show="showBreedButton">Cruzar</button>
      <div v-show="showBreedResult">
        <h3>Resultados:</h3>
        <div class="breed-results">
          <div class="breed-card" v-for="(dog, index) in breedResult" :key="index">
            <div class="breed-card-image">
              <img :src="dog.image" alt="Dog">
            </div>
            <div class="breed-card-info">
              <div class="breed-card-field">
                <label>Nome:</label>
                <input type="text" v-model="dog.name" @input="handleNameChange(index)" @blur="handleNameBlur(index)">
                <div v-show="showSaveButton[index]">
                  <button @click="saveName(dog.id, dog.name, index)">Salvar</button>
                  <button @click="cancelName(index)">Cancelar</button>
                </div>
              </div>
              <div class="breed-card-field">
                <label>Gênero:</label>
                <span>{{ dog.gender }}</span>
              </div>
              <div class="breed-card-field">
                <label>Raça:</label>
                <span>{{ dog.breed }}</span>
              </div>
              <div class="breed-card-field">
                <label>Altura:</label>
                <span>{{ dog.height }}</span>
              </div>
              <div class="breed-card-field">
                <label>Peso:</label>
                <span>{{ dog.weight }}</span>
              </div>
              <div class="breed-card-field">
                <label>Pai:</label>
                <span>{{ dog.father }}</span>
              </div>
              <div class="breed-card-field">
                <label>Mãe:</label>
                <span>{{ dog.mother }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "BreedModal",
  props: {
    showModal: Boolean,
    dogs: Array
  },
  data() {
    return {
      selectedFather: null,
      selectedMother: null,
      breedResult: [],
      showBreedButton: true,
      showBreedResult: false,
      showSaveButton: []
    };
  },
  computed: {
    males(){
      return this.dogs.filter(e => e.gender === 'M')
    },
    females(){
      return this.dogs.filter(e => e.gender === 'F')
    }
  },
  mounted() {
  document.addEventListener("keydown", this.handleKeyDown);
  document.addEventListener("click", this.handleOutsideClick);
  },
  beforeUnmount() {
    document.removeEventListener("keydown", this.handleKeyDown);
    document.removeEventListener("click", this.handleOutsideClick);
  },
  methods: {
    handleKeyDown(event) {
      if (event.keyCode === 27 && this.showModal) {
        this.closeModal();
      }
    },
    handleOutsideClick(event) {
      if (event.target.classList.contains("modal")) {
        this.closeModal();
      }
    },
    closeModal() {
      this.$emit("close");
    },
    breed() {
      // Call API to breed dogs and get result
      axios.post('http://localhost:5000/dogs/cross', { father: this.selectedFather, mother: this.selectedMother })
      .then(response => {
        this.breedResult = response.data;
      })
      .catch(error => {
        console.error(error);
      });
      // const result = api.breedDogs(this.father, this.mother);
      
      // Show the breed results section and hide the breed button
      this.showBreedResult = true;
      this.showBreedButton = false;

      // Initialize the showSaveButton array to false for each dog in the breedResult
      this.showSaveButton = new Array(this.breedResult.length).fill(false);
    },
    handleNameChange(index) {
      // Set the showSaveButton value to true for the corresponding dog card
      this.showSaveButton[index] = true;
    },
    handleNameBlur(index) {
      // Set the showSaveButton value to false for the corresponding dog card
      this.showSaveButton[index] = false;
    },
    saveName(id, name, index) {
      // Call API to update dog name
      // api.updateDogName(id, name);

      // Set the showSaveButton value to false for the corresponding dog card
      this.showSaveButton[index] = false;
    },
    cancelName(index) {
      // Reset the name input field to the original dog name
      this.breedResult[index].name = this.dogs.find(dog => dog.id === this.breedResult[index].id).name;

      // Set the showSaveButton value to false for the corresponding dog card
      this.showSaveButton[index] = false;
    }
  }
};
</script>

<style lang="scss">
  .modal {
    position: fixed;
    z-index: 999;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.4);
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .modal-content {
    background-color: white;
    width: 1000px;
    height: 500px;
    border-radius: 5px;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    position: relative;
  }

  .close {
    position: absolute;
    top: 20px;
    right: 20px;
    font-size: 24px;
    color: #aaa;
    cursor: pointer;
    &:hover {
      color: #333;
    }
  }

  // h2 {
  //   margin: 0;
  //   padding: 20px;
  //   background-color: #1c1c1c;
  //   color: white;
  //   border-top-left-radius: 5px;
  //   border-top-right-radius: 5px;
  // }

  .inputs {
    display: flex;
    justify-content: space-around;
    align-items: center;
    margin: 20px;
  }

  .input-group {
    display: flex;
    flex-direction: column;
  }

  label {
    margin-bottom: 5px;
  }

  select {
    height: 30px;
    font-size: 16px;
    padding: 0 10px;
    border-radius: 5px;
    border: 1px solid #ccc;
  }

  button {
    margin: 20px;
    padding: 10px 20px;
    background-color: #1c1c1c;
    color: white;
    border: none;
    border-radius: 5px;
    font-size: 16px;
    cursor: pointer;
  }

  .breed-results {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    margin-top: 20px;
  }

  .breed-card {
    width: 300px;
    height: 350px;
    margin: 10px;
    display: flex;
    flex-direction: column;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
    border-radius: 5px;
    overflow: hidden;
  }

  .breed-card-image {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    img {
      max-width: 100%;
      max-height: 100%;
      object-fit: cover;
    };
  }

  .breed-card-info {
    padding: 10px;
  }

  .breed-card-field {
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    label {
      font-weight: bold;
      margin-right: 10px;
    };
    input[type="text"] {
      flex: 1;
      &:focus {
        outline: none;
      };
    };
    button {
      margin-left: 10px;
      background-color: #1c1c1c;
      color: white;
      border: none;
      border-radius: 5px;
      padding: 5px 10px;
      cursor: pointer;
      &:hover {
        background-color: #555555;
      };
      &:active {
        transform: translateY(1px);
      };
    };
  }
</style>