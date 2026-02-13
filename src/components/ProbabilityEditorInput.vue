<template>
    <BaseInput
        ref="baseInputEl"
        class="probability-editor-input"
        :transformer="inputTransformer"
        :charPredicate="charPredicate"
        v-model="rawInput" />
</template>

<script setup>
import {
    computed,
    ref,
    useTemplateRef,
    watch
} from 'vue';

import BaseInput from './BaseInput.vue';

const baseInputEl = useTemplateRef('baseInputEl');
const innerElement = computed(() => {
    return baseInputEl.value?.innerElement;
});

const inputNumber = defineModel({ required: true });
const rawInput    = ref('');

/*
    Keep synced
*/
watch(inputNumber, () => rawInput.value    = inputNumber.value.toString(), { immediate: true });
watch(rawInput,    () => inputNumber.value = +rawInput.value,              { immediate: true });

function inputTransformer(str) {
    /*
        If string is empty, +"" === 0
    */
    return str.length
        ? Math.min(+str, 99999).toString()
        : str;
}

function charPredicate(c) {
    return c.charCodeAt(0) >= '0'.charCodeAt(0) &&
           c.charCodeAt(0) <= '9'.charCodeAt(0);
}

defineExpose({
    innerElement
});
</script>

<style scoped>
.probability-editor-input {
    background:    rgb(0, 0, 60);
    border-radius: 3px;
    border:        3px solid var(--col-outl-1);
    color:         white;
    justify-self:  center;
    padding:       7px 0;
    width:         120px;

    -webkit-text-stroke:  4px var(--col-outl-1);
    font-size:            22px;
    font-variant-numeric: tabular-nums;
    font-weight:          bold;
    letter-spacing:       0.06em;
    text-align:           center;

    box-shadow: var(--shadow-s);
}

.probability-editor-input:hover {
    border-color: rgb(255, 176, 254);
    background:   rgb(0, 0, 81);
}

.probability-editor-input:focus {
    background: rgb(2, 2, 87);

    box-shadow:
        inset 0 5px 8px var(--col-navy-2),
        var(--shadow-l);

    border-color: var(--col-hl-1);
}
</style>