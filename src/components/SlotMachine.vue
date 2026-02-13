<template>
    <div class="slot-machine">
        <div
            class="parts"
            :class="{ 'parts--intimidating': !isAnimating }">
            <SlotMachinePart1 class="slot-part slot-part--1" />
            <SlotMachinePart2 class="slot-part slot-part--2" />
            <SlotMachinePart3 class="slot-part slot-part--3" />
            <SlotMachinePart4 class="slot-part slot-part--4" />
            <SlotMachinePart5 class="slot-part slot-part--5" />

            <SlotMachinePartButton
                class="slot-part slot-part--button"
                :isHeldDown="isAnimating"
                @click="runSlotMachine" />

            <div class="cards">
                <SlotMachinePartCard1 class="slot-part slot-part--card slot-part--card--1" />
                <SlotMachinePartCard2 class="slot-part slot-part--card slot-part--card--2" />
                <SlotMachinePartCard3 class="slot-part slot-part--card slot-part--card--3" />

                <SlotMachineCardAnimation ref="cardAnimation1" class="card-overlay card-overlay--1" />
                <SlotMachineCardAnimation ref="cardAnimation2" class="card-overlay card-overlay--2" />
                <SlotMachineCardAnimation ref="cardAnimation3" class="card-overlay card-overlay--3" />
            </div>
        </div>
    </div>
</template>

<script setup>
import {
    ref,
    useTemplateRef
} from 'vue';

import SlotMachineCardAnimation from './SlotMachineCardAnimation.vue';

import SlotMachinePart1 from './SlotMachinePart1.vue';
import SlotMachinePart2 from './SlotMachinePart2.vue';
import SlotMachinePart3 from './SlotMachinePart3.vue';
import SlotMachinePart4 from './SlotMachinePart4.vue';
import SlotMachinePart5 from './SlotMachinePart5.vue';

import SlotMachinePartButton from './SlotMachinePartButton.vue';
import SlotMachinePartCard1 from './SlotMachinePartCard1.vue';
import SlotMachinePartCard2 from './SlotMachinePartCard2.vue';
import SlotMachinePartCard3 from './SlotMachinePartCard3.vue';

const props = defineProps({
    numerator: {
        type:     Number,
        required: true,
        validator(value, props) {
            return value <= props.denominator;
        }
    },

    denominator: {
        type:     Number,
        required: true,
        validator(value, props) {
            return value >= props.numerator && value >= 1;
        }
    }
});

const emit = defineEmits([
    'spinStarted',
    'spinCompleted'
]);

const cardAnimation1 = useTemplateRef('cardAnimation1');
const cardAnimation2 = useTemplateRef('cardAnimation2');
const cardAnimation3 = useTemplateRef('cardAnimation3');

const isAnimating = ref(false);

async function runSlotMachine() {
    const rng = Math.random;

    const pFail   = props.numerator / props.denominator;
    const didFail = rng() < pFail;

    /*
        False means a bad pretzel in that slot.
    */
    let values = [false, false, false];

    if (!didFail) {
        /*
            Bias toward "close calls".
        */
        const qBase = 0.85 * Math.pow(pFail, 0.7);
        const w     = [1.15, 1.0, 0.85];

        values = w.map(wi => !(rng() < Math.min(0.95, qBase * wi)));

        if (!values[0] && !values[1] && !values[2])
            values[2] = true;
    }

    emit('spinStarted');
    isAnimating.value = true;
    await Promise.all([
        cardAnimation1.value.animate(6000, values[0]),
        cardAnimation2.value.animate(7000, values[1]),
        cardAnimation3.value.animate(8000, values[2])]);
    isAnimating.value = false;

    emit('spinCompleted', !didFail);
    return !didFail;
}
</script>

<style scoped>
.slot-machine {
    container: slot-machine / size;
    display: grid;
    place-content: center;
}

.parts {
    --x-rotation: 0deg;
    --scale:      1;

    --part-1-offset: 0px;
    --part-2-offset: 0px;
    --part-3-offset: 0px;
    --part-4-offset: 0px;
    --part-5-offset: 0px;

    --part-cards-offset:  0px;
    --part-button-offset: 0px;

    --transform-transition: transform 1000ms ease;

    aspect-ratio:   0.7 / 1;
    display:        grid;
    grid-area:      1 / 1;
    height:         600px;
    pointer-events: none;
    z-index:        0;

    transform:
        perspective(400px)
        rotateX(var(--x-rotation))
        scale(var(--scale));

    animation:  none;
    transition: var(--transform-transition);
}

.parts--intimidating {
    --x-rotation: 5deg;

    --part-1-offset: -2px;
    --part-2-offset: -8px;
    --part-3-offset: -5px;
    --part-4-offset: -10px;
    --part-5-offset: -13px;

    --part-cards-offset:  -7px;
    --part-button-offset: -7px;
}

.slot-part {
    grid-area:      1 / 1;
    pointer-events: none;
    transition:     var(--transform-transition);
}

.slot-part--1 {
    z-index:   0;
    transform: translateY(var(--part-1-offset));
}

.slot-part--2 {
    z-index:   1;
    transform: translateY(var(--part-2-offset));
}

.slot-part--3 {
    z-index:   0;
    transform: translateY(var(--part-3-offset));
}

.slot-part--4 {
    z-index:   1;
    transform: translateY(var(--part-4-offset));
}

.slot-part--5 {
    z-index:   2;
    transform: translateY(var(--part-5-offset));
}

.slot-part--button {
    pointer-events: all;
    z-index:        2;
    transform:      translateY(var(--part-button-offset));
}

.cards {
    display:   grid;
    grid-area: 1 / 1;
    z-index:   2;

    transform:  translateY(var(--part-cards-offset));
    transition: var(--transform-transition);
}

.slot-part--card {
    z-index: 0;
}

.card-overlay {
    grid-area:  1 / 1;
    place-self: center;
    z-index:    1;
    
    transform: 
        translate(
            calc(2px   + var(--center-offset-x)), 
            calc(-69.7px + var(--center-offset-y)));
}

.card-overlay--1 { --center-offset-x: -79px; --center-offset-y: 0px; }
.card-overlay--2 { --center-offset-x: 0px;   --center-offset-y: 0px; }
.card-overlay--3 { --center-offset-x: 77px;  --center-offset-y: 0px; }

@container slot-machine ((max-width: 700px) or (max-height: 670px)) {
    .parts {
        --scale: 0.8;
    }
}

@container slot-machine ((max-width: 400px) or (max-height: 500px)) {
    .parts {
        --scale: 0.7;
    }
}
</style>