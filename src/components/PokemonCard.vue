<template>

    <div class="card" :style="cssVars">
        <div class="ladoEsquerdo">
            <div class="nomePokemon"> {{ name }} </div>
            <div class="tipoPokemon" v-for="item in typeList" :key="item">
                <div class="typexx">
                    <p>
                        {{ pokemonTypeDictionary(item.type.name, "name") }}
                    </p>
                </div>
            </div>
        </div>
        <div class="ladoDireito">
            <div class="idPokemon"> {{ pokemonIdSintax(pokemonUrl.id) }} </div>
            <div class="foto"> <img v-bind:src="pokemonUrl.sprites?.front_default"></div>
        </div>

    </div>
</template>
<script lang="ts">
import { defineComponent } from 'vue';
import axios from "axios";
import { pokemonTypeDictionary } from '@/utils';
export default defineComponent({
    props: {
        name: String,
        url: String,
        img: String
    },
    data() {
        return { pokemonUrl: {} as any, pokemonId: "" as any, pokemonType: "" as any, typeList: [] as any, typeColor: "" as string, backgroundTypeColor: "white" as string, fontTypeColor: "black" }
    },
    beforeMount() {
        axios.get(`${this.url}`)
            .then(response => {
                this.pokemonUrl = JSON.parse(JSON.stringify(response.data))
                this.typeList = this.pokemonUrl.types
                this.typeColor = this.pokemonTypeDictionary(this.typeList[0].type.name, "color")
            })
            .catch(error => {
                console.error(error)
            })
    },
    methods: {
        pokemonIdSintax(crudeId: number) {
            if (crudeId < 10) {
                return `#00${crudeId}`
            } else if (crudeId < 100) {
                return `#0${crudeId}`
            } else if (crudeId < 1000) {
                return `#${crudeId}`
            }
        },
        pokemonTypeDictionary(type: keyof typeof pokemonTypeDictionary, propName: "name" | "color") {
            return pokemonTypeDictionary[type][propName]
        },
    },
    computed: {
        cssVars() {
            return {
                '--bg-color': this.typeColor,
                '--type_bg-color': this.backgroundTypeColor,
                '--font_bg-color': this.fontTypeColor
            }
        },
    }
})
</script>
<style lang="scss">
.card {
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@500;600&display=swap');
    box-sizing: border-box;
    width: 100%;
    height: 160px;
    border-radius: 15px;
    display: flex;
    flex-direction: row;
    background-color: var(--bg-color);

    padding-right: 10px;

    .ladoEsquerdo {
        width: 45%;
    }

    .ladoDireito {
        width: 55%;
    }

    .idPokemon {
        font-family: 'Inter', sans-serif;
        font-weight: 500;
        padding-right: 9px;
        padding-top: 9px;
        display: flex;
        justify-content: flex-end;
        opacity: 0.3;
    }

    .nomePokemon {
        font-family: 'Inter', sans-serif;
        font-weight: 600;
        margin-top: 30px;
        padding: 0px 0px 0px 15px;
        text-transform: capitalize;
        color: white;
    }

    .tipoPokemon {
        display: grid;

        color: var(--font_bg-color);
        background-color: var(--type_bg-color);
        opacity: 0.2;
        border-radius: 38px;
        margin-left: 13px;
        margin-top: 5px;
        padding: 0px 4.5px 0px 4.5px;

        .typexx {


            p {
                margin-left: 10px
            }
        }

    }

    .foto {

        display: flex;
        justify-content: flex-end;
        padding-right: 5px;
        width: 135px;
        height: 130px;

        img {
            width: 100%;
            height: 100%;
        }


    }
}
</style>