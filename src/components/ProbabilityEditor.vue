<template>
    <div
        class="probability-editor"
        @focusin="gotFocus"
        @focusout="lostFocus">
        <ProbabilityEditorInput
            ref="numeratorEl"
            v-model="inputNumerator"
            @keydown.enter="submit"
            aria-label="Numerator" />
        <div class="in">IN</div>
        <ProbabilityEditorInput
            ref="denominatorEl"
            v-model="inputDenominator"
            @keydown.enter="submit"
            aria-label="Denominator" />
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

const emit = defineEmits([
    'interacted',
    'focus-in',
    'focus-out'
]);

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

watch([inputNumerator, inputDenominator], () => {
    emit('interacted');
});

function gotFocus(e) {
    if (e.relatedTarget !== numeratorEl.value.innerElement &&
        e.relatedTarget !== denominatorEl.value.innerElement) {
        emit('focus-in');
    }
}

function lostFocus(e) {
    if (e.relatedTarget !== numeratorEl.value.innerElement &&
        e.relatedTarget !== denominatorEl.value.innerElement) {
        inputDenominator.value = denominator.value = realDenominator.value;
        inputNumerator.value   = numerator.value   = realNumerator.value;
        emit('focus-out');
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