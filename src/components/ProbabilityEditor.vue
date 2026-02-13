<template>
    <div
        class="probability-editor"
        @focusout="lostFocus">
        <ProbabilityEditorInput
            ref="numeratorEl"
            v-model="inputNumerator"
            @keydown.enter="submit" />
        <div class="in">IN</div>
        <ProbabilityEditorInput
            ref="denominatorEl"
            v-model="inputDenominator"
            @keydown.enter="submit" />
    </div>
</template>

<script setup>
import {
    computed,
    ref,
    useTemplateRef,
    watch
} from 'vue';

import ProbabilityEditorInput from './ProbabilityEditorInput.vue';

const numerator = defineModel('numerator', {
    required: true,
    type:     Number
});

const denominator = defineModel('denominator', {
    required: true,
    type:     Number
});

const numeratorEl   = useTemplateRef('numeratorEl');
const denominatorEl = useTemplateRef('denominatorEl');

const inputNumerator   = ref(1);
const inputDenominator = ref(10);

const realNumerator   = computed(() => Math.min(inputNumerator.value,   realDenominator.value));
const realDenominator = computed(() => Math.max(inputDenominator.value, 1));

watch([numerator, denominator], () => {
    inputNumerator.value   = numerator.value;
    inputDenominator.value = denominator.value;
}, {
    immediate: true
});

function lostFocus(e) {
    if (e.relatedTarget !== numeratorEl.value.innerElement &&
        e.relatedTarget !== denominatorEl.value.innerElement) {
        inputDenominator.value = denominator.value = realDenominator.value;
        inputNumerator.value   = numerator.value   = realNumerator.value;
    }
}

function submit() {
    numeratorEl.value.innerElement.blur();
    denominatorEl.value.innerElement.blur();
}
</script>

<style scoped>
.probability-editor {
    align-items: center;
    display:     flex;
    gap:         18px;
    margin-top:  37px;
    paint-order: stroke;
}

.in {
    color:        white;
    font-weight:  bold;
    margin-right: -0.3em;
    user-select:  none;
    
    -webkit-text-stroke: 4px var(--col-outl-1);
    letter-spacing:      0.18em;
    
    paint-order: stroke;
}
</style>