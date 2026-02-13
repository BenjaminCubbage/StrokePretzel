<template>
    <div class="app-container">
        <ProbabilityEditor
            class="probability"
            v-model:numerator="numerator"
            v-model:denominator="denominator" />

        <SlotMachine
            class="slot-machine"
            :numerator="numerator"
            :denominator="denominator"
            @spinStarted="spinStarted"
            @spinCompleted="spinCompleted" />

        <div class="bg-floor">
            <div class="grid-pattern"></div>
        </div>
    </div>
</template>

<script setup>
import {
    ref,
    watch
} from 'vue';

import SlotMachine       from './components/SlotMachine.vue';
import ProbabilityEditor from './components/ProbabilityEditor.vue';

const numerator   = ref(1);
const denominator = ref(100);

/*
    If the probability was edited while spinning, don't
    auto-decrement.
*/
let wasEdited = false;

watch([numerator, denominator], () => {
    wasEdited = true;
});

function spinStarted() {
    wasEdited = false;
}

function spinCompleted(won) {
    if (!wasEdited && denominator.value > 1) {
        if (denominator.value > 1)
            --denominator.value;

        if (!won)
            --numerator.value;
    }
}
</script>

<style scoped>
.app-container {
    display:  grid;

    grid-template-columns: [probability slot-machine bg] 1fr;
    grid-template-rows:    [probability slot-machine bg] 1fr;

    height:   100dvh;
    overflow: hidden;
    z-index:  0;

    background:
        radial-gradient(
            ellipse at 50% 100%,
            var(--col-navy-2) 20%,
            var(--col-navy-1) 80%);
}

.probability {
    grid-area:    probability;
    align-self:   start;
    justify-self: center;
    z-index:      3;
}

.slot-machine {
    grid-area:  slot-machine;
    place-self: stretch;
    z-index:    2;
}

.bg-floor {
    grid-area:  bg;
    place-self: stretch;
    overflow:   hidden;
    position:   relative;
    mask:       radial-gradient(at 50% 100%, black, transparent 80%);
    z-index:    1;
}

.grid-pattern {
    background-image:
        linear-gradient(to right, rgb(255, 0, 111)  3px, transparent 3px),
        linear-gradient(to bottom, rgb(251, 0, 255) 3px, transparent 3px);

    transform:
        perspective(50px)
        rotateX(20deg);

    height:          100%;
    width:           100%;
    background-size: 40px 40px;
}

@media only screen and (max-height: 650px) {
    .app-container {
        grid-template-columns: [probability slot-machine bg] 1fr;
        grid-template-rows:    [probability bg-start] 40px [slot-machine] 1fr [bg-end];
    }
}

@media only screen and (max-height: 550px) {
    .app-container {
        grid-template-columns: [probability slot-machine bg] 1fr;
        grid-template-rows:    [probability bg-start] 60px [slot-machine] 1fr [bg-end];
    }
}

@media only screen and (max-height: 500px) {
    .app-container {
        grid-template-columns: [probability slot-machine bg] 1fr;
        grid-template-rows:    [probability bg-start] 100px [slot-machine] 1fr [bg-end];
    }
}
</style>