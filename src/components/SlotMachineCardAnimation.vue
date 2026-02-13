<template>
    <div class="slot-machine-card-animation">
        <div
            class="reel"
            :style="`transform: translateY(${reelOffset}px)`">
            <SlotMachinePretzelGood class="symbol symbol--1" />
            <SlotMachinePretzelGood class="symbol symbol--2" />
            <SlotMachinePretzelGood class="symbol symbol--3" />
            <SlotMachinePretzelEvil class="symbol symbol--4" />
            <SlotMachinePretzelGood class="symbol symbol--5" />
            <SlotMachinePretzelGood class="symbol symbol--6" />
            <SlotMachinePretzelGood class="symbol symbol--7" />
        </div>
    </div>
</template>

<script setup>
import {
    onUnmounted,
    reactive,
    ref
} from 'vue';

import SlotMachinePretzelGood from './SlotMachinePretzelGood.vue';
import SlotMachinePretzelEvil from './SlotMachinePretzelEvil.vue';

const minReelOffset = -100;
const maxReelOffset = 101;

const reelOffsetEvil  = 0;
const reelOffsetGood1 = -49;
const reelOffsetGood2 = 50;

const reelOffset = ref(reelOffsetGood1);

/*
    Call resolve() when cancelling a scheduled frame so promises
    don't wait forever.
*/
const scheduledFrame = reactive({
    handle:  null,
    resolve: null
});

/*
    Run whole animation with predetermined outcome.
*/
async function animate(duration, goodResult = true) {
    const keyframeFinalOffset =
        !goodResult
            ? reelOffsetEvil
            : Math.random() > 0.3
                ? reelOffsetGood1
                : reelOffsetGood2;

    const numRotations = 5 + Math.floor(duration / 1000);

    const minCorrection = -23;
    const maxCorrection = 24;

    const correction = Math.random() * (maxCorrection - minCorrection) + minCorrection;

    const timeline = {
        start:             performance.now(),
        smootherStepEnd:   performance.now() + Math.max(duration - 800, 0),
        correctionJoltEnd: performance.now() + duration
    };

    const keyframes = {
        start:             reelOffset.value,
        smootherStepEnd:   (maxReelOffset - minReelOffset) * numRotations + keyframeFinalOffset + correction,
        correctionJoltEnd: (maxReelOffset - minReelOffset) * numRotations + keyframeFinalOffset
    };

    await animateBetween(
        keyframes.start,
        keyframes.smootherStepEnd,
        timeline.start,
        timeline.smootherStepEnd);

    await animateBetween(
        keyframes.smootherStepEnd,
        keyframes.correctionJoltEnd,
        timeline.smootherStepEnd,
        timeline.correctionJoltEnd);
}

/*
    Cancel animation if running.
*/
async function cancelAnimation() {
    cancelAnimationFrame(scheduledFrame.handle);
    scheduledFrame.resolve();
}

/*
    Animate reel offset between two values over timeframe.

    The "to" argument can be modulo reduced (wrap around).
*/
async function animateBetween(from, to, startTime, endTime) {
    return new Promise(resolve => {
        function nextFrame() {
            const now  = performance.now();
            const perc = (now - startTime) / (endTime - startTime);

            reelOffset.value = modularReduce(
                smoothInterpolate(from, to, perc),
                minReelOffset,
                maxReelOffset);

            if (perc < 1)
                scheduledFrame.handle = requestAnimationFrame(nextFrame);
            else
                resolve();
        }

        scheduledFrame.resolve = resolve;
        scheduledFrame.handle  = requestAnimationFrame(nextFrame);
    });
}

/*
    A smoothing function that is not very good.
*/
function smoothInterpolate(from, to, percentage) {
    const smoothStep = (x) => {
        const xTo2 = x * x;
        const xTo3 = xTo2 * x;

        return (xTo3 / (xTo3 + Math.pow((1 - x), 20))) * (xTo3 * (x * (( x * 6) - 15) + 10));
    }

    if (percentage <= 0) return from;
    if (percentage >= 1) return to;

    return from + (to - from) * smoothStep(percentage, 0, 1);
}

/*
    Modular reduction, inclusive.

    I don't actually think this is correct but it seems to work.
*/
function modularReduce(value, rangeStart, rangeEnd) {
    return (value - rangeStart) % (rangeEnd - rangeStart) + rangeStart;
}

onUnmounted(cancelAnimation);

defineExpose({
    animate,
    cancelAnimation
});
</script>

<style scoped>
.slot-machine-card-animation {
    --container-height: 100px;
    --symbol-height: 45px;

    position: relative;

    display: flex;
    flex-direction: column;
    align-items: center;
    width: var(--symbol-height);
    height: 99px;
    overflow: hidden;
}

.reel {
    position: absolute;
    top: -124px;
}

.symbol {
    height: var(--symbol-height);
}
</style>