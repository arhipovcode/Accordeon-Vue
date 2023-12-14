<template>
    <h1 class="title">Аккордеон (Uses Typescript)</h1>
    <Accordion :accordion-data="accrdData"/>
</template>

<script lang="ts">
import {onMounted, reactive} from "vue";
import Accordion from "./components/Accordion.vue";
import {AccordionData} from "./types/AccordionData.ts";

export default {
    components: {Accordion},

    setup() {
        const accrdData = reactive<AccordionData[]>([])
        const addMockData = (count : number) : any => {
            Array.from({ length: count }, (_, i) => {
                accrdData.push({
                    title: 'Заголовок ' + (i + 1),
                    text:  + (i + 1) + ': Здесь будет какой-то текст!'
                })
            });
        }
        onMounted(() => {
            addMockData(10);
        })
        return {
            accrdData,
        }
    }
}
</script>
