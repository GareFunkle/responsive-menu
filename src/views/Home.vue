<template>
  <main class="home-page">
    <header>
      <div class="wrap-header-img">
        <img class="header-img" src="../assets/Logo/logo-UTB-stock.png" alt="">
      </div>
      <Search />
    </header>
    <div class="wrap-card">
      <Card v-for="product in allproducts" :sub="product.subproducts" :card="product" :key="product.id" />
    </div>
    <Footer />
  </main>
</template>

<script>
import { onMounted, ref } from 'vue';

import { collection, onSnapshot, orderBy, query } from 'firebase/firestore'

import { db } from '../Firebase/firebase.js'

import Card from '../components/Card.vue';
import Search from '../components/Search.vue';
import Footer from '../components/Footer.vue';


export default {
  name: 'Home',
  components: {
    Card,
    Search,
    Footer
  },

  setup() {


    const allproducts = ref([])

    onMounted(() => {
      const q = query(collection(db, 'products'), orderBy('stock', "desc"))


      onSnapshot(q, (querySnapshot) => {
        const fetchedProducts = [];

        querySnapshot.forEach((doc) => {
          fetchedProducts.push({ id: doc.id, ...doc.data() })
        })
        allproducts.value = fetchedProducts
      });


    })

    return {
      allproducts,

    }
  },

}
</script>

<style lang="scss" scoped>
.home-page {

  header {

    .wrap-header-img {
      padding: 1rem;
      display: flex;
      justify-content: center;
      align-items: center;
      position: relative;
      z-index: 3;

      .header-img {
        background-size: cover;
        position: center;
        width: 50%;
        // filter: drop-shadow();
        filter: drop-shadow(0px 0px 3px var(--logo-letters));
        animation: zoom 2s ease-in-out;

        @media (max-width: 760px) {
          width: 100%;
          filter: drop-shadow(0px 0px 1px var(--logo-letters));
        }

        @keyframes zoom {
          0% {
            // transform: scale(0);
            opacity: 0;
          }
          100% {
            // transform: scale(100%);
            opacity: 1;
          }
        }
      }

    }

    .wrap-icons {
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
    }
  }

  .wrap-card {
    display: flex;
    flex-wrap: wrap;
    gap: 2rem 1rem;
    justify-content: center;

    @media (max-width: 760px) {
      flex-direction: column;

    }
  }
}
</style>