<template>
  <main class="bois-page">
    <div class="return">
      <span @click="goHome()" class="material-icons">arrow_back</span>
    </div>
    <div class="wrap-title">
      <h1> {{ $route.params.category.replace(/(?:^|\s|-)\S/g, x => x.toUpperCase()) }} </h1>
    </div>
    <Search />
    <div class="cards-grid">
      <Card  v-for="product in products" :card="product" :key="product.id" />
    </div>
  </main>
</template>

<script>
import { onMounted, ref } from 'vue';
import { useRoute } from "vue-router"

import Card from '../components/Card.vue';


import { collection, query, where,  onSnapshot } from 'firebase/firestore'

import { db } from '../Firebase/firebase.js'
import Search from '../components/Search.vue';

export default {
  name: 'Products',
  components: {
    Card,
    Search
  },
  methods: {
    goHome() {
      this.$router.push('/')
    }
  },
  setup() {
    const products = ref([]);

    onMounted(async () => {
      const route = useRoute()

      const q = query(collection(db, "products"), where("category", "==", route.params.category))

      onSnapshot(q, (querySnapshot) => {
        const fetchedProducts = [];

        querySnapshot.forEach((doc) => {
          fetchedProducts.push({ id: doc.id, ...doc.data() })
        })
        products.value = fetchedProducts
      });
    })


    return {
      products,
    }
  },
}
</script>

<style lang="scss" scoped>
.bois-page {

  .return {
    position: relative;
    z-index: 1;
    margin: 1rem;
  }

  .material-icons {
    background: rgba(0, 0, 0, 0.9);
    padding: 0.4rem;
    border-radius: 5px;
    font-size: 2.5rem;
    color: var(--light);
    cursor: pointer;
    transition: 0.2s;
    border: 2px solid var(--logo-letters);

    &:hover {
      color: var(--logo-letters);
      filter: drop-shadow(0px 0px 10px var(--logo-letters));
      transform: translateX(-0.5rem) scale(1.1, 1.1);
      transition: 0.2s ease-out;
    }

    .button {
      text-decoration: none;
    }
  }

  .wrap-title {
    display: flex;
    justify-content: center;
    width: 100%;

  }

  .test {
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
  }

  .cards-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 2rem 1rem;
    justify-content: center;

    @media (max-width: 760px) {
      .cards-grid {
        flex-direction: column;
      }
    }
  }


}
</style>
