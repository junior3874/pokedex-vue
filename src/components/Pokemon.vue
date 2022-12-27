<template>

    <div class="divBusca">
        <input v-model='search' type="text" id="txtBusca" placeholder="Buscar..." @change="current_Data" />
        <img src="../assets/search3.png.png" id="btnBusca" alt="Buscar" />
    </div>

    <div class="container">

        <div class="pokemon-card-wrapper" v-if="!search">
            <PokemonCard v-for="(pokemon) in pkm" :name="pokemon.name" :url="pokemon.url" v-bind:key="pokemon.name" />
        </div>

        <div v-else class="pokemon-card-wrapper">
            <PokemonCard v-for="(pokemon) in pkmFiltredList" :name="pokemon.name" :url="pokemon.url"
                v-bind:key="pokemon.name" />
        </div>

    </div>

    <div class="buttons">
        <div class="back-button" v-if="page !== 1">
            <button @click="updatePagination('previous')">
                <p class="button-label">
                    PREVIOUS PAGE
                </p>
            </button>
        </div>

        <div class="next-button" v-if="page !== 9">
            <button @click="updatePagination('next')">
                <p class="button-label">
                    NEXT PAGE
                </p>
            </button>
        </div>
    </div>



</template>

<script lang="ts">
import { defineComponent } from 'vue';
import api from '@/services/api';
import PokemonCard from './PokemonCard.vue';
type Pokemon = {
    name: string;
    url: string;
}
export default defineComponent({
    name: "Pokemon-vue",
    components: {
        PokemonCard
    },
    data() {
        return { pkm: [] as Pokemon[], offset: 0, page: 1, search: "", pkmCompleteList: [] as Pokemon[], pkmFiltredList: [] as Pokemon[] }
    },
    beforeMount() {
        api.get(`/pokemon?limit=18&offset=${this.offset}`)
            .then(response => {
                this.pkm = response.data.results;
            });
    },
    methods: {
        updatePagination(x: string) {
            if (x == 'previous' && this.page !== 1) {
                this.offset -= 18
                this.page--

            } else if (x == 'next' && this.page !== 9) {
                this.offset += 18
                this.page++
            }
            else {
                const limit = this.page === 9 ? 7 : 18;
                api.get(`/pokemon?limit=${limit}&offset=${this.offset}`)
                    .then(response => {
                        this.pkm = response.data.results
                    });

            }

        },
        async current_Data() {

            if (this.pkmCompleteList.length === 0) {
                const res =
                    await api.get(`/pokemon?limit=151&offset=0`)
                        .then(res =>
                            res.data
                        ).catch(err => ({
                            error: true,
                            message: err
                        }));

                if (res?.error) return

                this.pkmCompleteList = res.results
                const result = res.results.filter((element: Pokemon) => element.name.toLowerCase().includes(this.search.toLowerCase()));
                return this.pkmFiltredList = result
            }

            const result = this.pkmCompleteList.filter(element => element.name.toLowerCase().includes(this.search.toLowerCase()));
            return this.pkmFiltredList = result
        }

    },
    computed: {

    }
})
</script>

<style lang="scss">
.pokemons {
    display: flex;
    flex-direction: column;
}

.divBusca {
    margin-left: 16px;
    border: solid 1px;
    border-radius: 15px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    width: 100%;
    max-width: 300px;

    #txtBusca {
        float: left;
        background-color: transparent;
        padding-left: 5px;
        font-style: italic;
        font-size: 18px;
        border: none;
        height: 100%;
        width: 100%;
        outline: none;
    }
}


.container {
    .pokemon-card-wrapper {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
        grid-gap: 20px;
        padding: 20px;
    }
}

.buttons {
    display: flex;
    justify-content: space-between;
    padding: 20px;

    button {
        background-color: black;
        border-radius: 20px;
        color: white;
        padding: 20px;
    }

    .button-label {
        font-size: 20px;
    }


}
</style>