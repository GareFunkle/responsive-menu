<template>
    <div class="add-sub-product">
        <div class="wrap-element">
            <span @click="isOpen = !isOpen" class="material-icons open">
                post_add
            </span>
            <p class="title-logo">Ajouter un sous produit</p>
        </div>

        <div v-if="isOpen" class="cart-add-product">
            <div class="wrap-icon">
                <span @click="isOpen = !isOpen" class="material-icons close">
                    cancel
                </span>

            </div>

            <h3>Ajouter un sous produit</h3>
            <div  class="forms">
                <select required>
                    <option value="" disabled selected hidden>Choisir une catégorie</option>
                    <optgroup v-for="productc in products" :key="productc.id" :label="productc.category">
                        <option > {{ productc.name }}</option>
                    </optgroup>
                </select>
                <input v-model="addTitle" type="text">
                <input v-model="addTotal" type="number" placeholder="*Totale">
                <button @click="addSubProducts()">Créer le sous produit</button>
            </div>
        </div>
    </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import { collection, addDoc, query, onSnapshot, orderBy, doc } from "firebase/firestore";
import { db } from '../Firebase/firebase.js'
import { useToast } from 'vue-toastification'
import { useRoute } from "vue-router"

export default {
    name: "AddSubProduct",
    data() {
        return {
            isOpen: false
        }
    },

    setup() {
        const toast = useToast()

        const addTitle = ref('')
        const addTotal = ref()

        const products = ref([]);

        onMounted(async () => {

            const q = query(collection(db, "products"), orderBy("category", "asc"))

            onSnapshot(q, (querySnapshot) => {
                const fetchedProducts = [];

                querySnapshot.forEach((doc) => {
                    fetchedProducts.push({ id: doc.id, ...doc.data() })
                })
                products.value = fetchedProducts
            });
        })

        const addSubProducts = async () => {
            const route = useRoute()
            await addDoc(collection(db, "products", route.params.id, "subproducts"), {
                title: addTitle.value,
                total: addTotal.value,

            });
            toast.success("Sous produit créer avec succes")
            addSubProducts ? addTitle.value = '' : addTitle.value = addTitle.value
            addSubProducts ? addTotal.value = '' : addTotal.value = addTotal.value

        }





        return {
            addTitle,
            addTotal,
            products,
            addSubProducts,
        }
    }

}
</script>

<style lang="scss">
.add-sub-product {

    .wrap-element {
        display: flex;
        align-items: center;

        .open {
            font-size: 2rem;
            color: var(--light);
            transition: 0.2s ease-out;
        }

        .title-logo {
            margin-left: 1rem;
            color: var(--light);
            font-weight: 600;
            transition: 0.2s ease-out;
        }

        &:hover {

            .open,
            .title-logo {
                color: var(--logo-letters);
            }
        }
    }



    .cart-add-product {
        position: fixed;
        border: 3px solid var(--logo-letters);
        background: var(--black);
        width: 30rem;
        height: 36rem;
        top: 25%;
        left: 40%;
        display: flex;
        flex-direction: column;
        padding: 1rem;
        border-radius: 5px;
        z-index: 10;

        @media (max-width: 768px) {
            left: 20.5%;
            width: 18rem;
        }

        @media (min-width: 768px) {
            left: 25%;
        }

        select {
            // padding: 1rem;
            border: none;
            color: black;
            border-radius: 5px;
            font-weight: bold;
            font-size: 1rem;
        }

        input {
            padding: 1rem;
            border: none;
            color: black;
            border-radius: 5px;
            font-weight: bold;
            font-size: 1rem;
        }

        .wrap-icon {
            .close {
                position: relative;
                float: right;
                background: var(--light);
                color: var(--logo-letters);
                padding: 0.3rem;
                border-radius: 5px;
                transition: color 0.2s, transform 0.3s;
                cursor: pointer;

                &:hover {
                    color: red;
                    // transform: scale(1.1, 1.1);

                    transition: 0.2s ease-out;
                }
            }
        }

        h3 {
            display: flex;
            justify-content: center;
        }

        .forms {
            display: flex;
            flex-direction: column;
            justify-content: center;

            select {
                height: 1.5rem;
                margin: 1rem;
            }

            input {
                margin: 1rem;
                height: 1.5rem;
            }

            button {
                margin: 1rem;
                background: var(--light);
                padding: 1rem;
                font-size: 1.3rem;
                font-weight: bold;
                transition: background 0.3s;
                border-radius: 5px;

                &:hover {
                    background: var(--logo-letters);
                    color: white;
                }
            }
        }

    }
}
</style>