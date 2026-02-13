<template>
    <div 
        class="slot-machine-part-button"
        :class="{ 
            'slot-machine-part-button--hovered':   isHovered, 
            'slot-machine-part-button--held-down': isHeldDown
        }"
        @mousemove="checkHover"
        @mousedown="checkPress"
        @mouseup="unpress"
        @click="checkClick"
        @focus="checkFocus"
        @keydown.space="click"
        @keydown.enter="click"
        tabindex="0">
        <svg
            v-show="!isDown"
            class="button-normal"
            style="height: 100%"
            viewBox="0 0 36.5 52.3">
            <ellipse class="st0" cx="18.2" cy="30.7" rx="5.3" ry="1.5"/>
            <path class="st1" d="M18.2,31.8c-2.5,0-5.1-0.6-5.1-1.7v-1.5H16c0.7-0.1,1.5-0.2,2.3-0.2s1.6,0.1,2.3,0.2l2.8,0v1.5
                C23.3,31.2,20.7,31.8,18.2,31.8z"/>
            <path class="st3" d="M18.2,28.5c0.8,0,1.6,0.1,2.3,0.2h2.7v1.4l0,0c0,0.9-2.2,1.6-4.9,1.6c-2.7,0-4.9-0.7-4.9-1.6v-1.4H16
                C16.6,28.6,17.4,28.5,18.2,28.5 M18.2,28.2c-0.8,0-1.6,0.1-2.3,0.2l-2.6,0H13v0.3v1.4c0,1.2,2.6,1.9,5.2,1.9
                c2.6,0,5.2-0.6,5.2-1.9l0-1.4v-0.3h-0.3h-2.7C19.8,28.3,19,28.2,18.2,28.2L18.2,28.2z"/>
            <path class="st2" d="M23.2,29.8c0,0.9-2.2,1.6-4.9,1.6c-2.7,0-4.9-0.7-4.9-1.6v0.3c0,0.9,2.2,1.6,4.9,1.6c2.7,0,4.9-0.7,4.9-1.6
                c0,0,0,0,0,0V29.8z"/>
            <path class="st1" d="M18.2,30.3c-2.5,0-5.1-0.6-5.1-1.7c0-1.1,2.6-1.7,5.1-1.7c2.5,0,5.1,0.6,5.1,1.7
                C23.3,29.7,20.7,30.3,18.2,30.3z"/>
            <path class="st3" d="M18.2,27.1c2.7,0,4.9,0.7,4.9,1.6c0,0.9-2.2,1.6-4.9,1.6c-2.7,0-4.9-0.7-4.9-1.6
                C13.3,27.8,15.5,27.1,18.2,27.1 M18.2,26.8c-2.6,0-5.2,0.6-5.2,1.9c0,1.2,2.6,1.9,5.2,1.9s5.2-0.6,5.2-1.9
                C23.5,27.4,20.8,26.8,18.2,26.8L18.2,26.8z"/>
            <path class="st4" d="M18.2,27.1c-2.7,0-4.9,0.7-4.9,1.6c0.5-0.5,2.8-1.2,4.9-1.2s4.4,0.6,4.9,1.2C23.2,27.8,21,27.1,18.2,27.1z"/>
            <path class="st2" d="M18.2,30.2c2.7,0,4.9-0.7,4.9-1.6c-0.5,0.5-2.8,1.2-4.9,1.2s-4.4-0.6-4.9-1.2C13.3,29.5,15.5,30.2,18.2,30.2z
                "/>
        </svg>

        <svg
            v-show="isDown"
            class="button-pressed"
            style="height: 100%"
            viewBox="0 0 36.5 52.3">
            <ellipse class="st0" cx="18.2" cy="30.7" rx="5.3" ry="1.5"/>
            <path class="st1" d="M13.1,29.4v0.7c0,1.1,2.6,1.7,5.1,1.7c2.5,0,5.1-0.6,5.1-1.7v-0.7H13.1z"/>
            <path class="st2" d="M23.2,29.8c0,0.9-2.2,1.6-4.9,1.6c-2.7,0-4.9-0.7-4.9-1.6v0.3c0,0.9,2.2,1.6,4.9,1.6c2.7,0,4.9-0.7,4.9-1.6
                c0,0,0,0,0,0V29.8z"/>
            <path class="st1" d="M18.2,31.1c-2.5,0-5.1-0.6-5.1-1.7c0-1.1,2.6-1.7,5.1-1.7c2.5,0,5.1,0.6,5.1,1.7
                C23.3,30.5,20.7,31.1,18.2,31.1z"/>
            <path class="st3" d="M18.2,27.8c2.7,0,4.9,0.7,4.9,1.6c0,0.9-2.2,1.6-4.9,1.6c-2.7,0-4.9-0.7-4.9-1.6
                C13.3,28.5,15.5,27.8,18.2,27.8 M18.2,27.5c-2.6,0-5.2,0.6-5.2,1.9c0,1.2,2.6,1.9,5.2,1.9s5.2-0.6,5.2-1.9
                C23.5,28.1,20.8,27.5,18.2,27.5L18.2,27.5z"/>
            <path class="st4" d="M18.2,27.8c-2.7,0-4.9,0.7-4.9,1.6c0.5-0.5,2.8-1.2,4.9-1.2s4.4,0.6,4.9,1.2C23.2,28.5,21,27.8,18.2,27.8z"/>
            <path class="st2" d="M18.2,30.9c2.7,0,4.9-0.7,4.9-1.6c-0.5,0.5-2.8,1.2-4.9,1.2s-4.4-0.6-4.9-1.2C13.3,30.2,15.5,30.9,18.2,30.9z"
                />
            <path class="st3" d="M23.2,29.4v0.7l0,0c0,0.9-2.2,1.6-4.9,1.6c-2.7,0-4.9-0.7-4.9-1.6v-0.7H13v0.7c0,1.2,2.6,1.9,5.2,1.9
                c2.6,0,5.2-0.6,5.2-1.9l0-0.7H23.2z"/>
        </svg>
    </div>
</template>

<script setup>
import { computed, ref } from 'vue';

const props = defineProps({
    /*
        Force button to be in held-down state, which disables it.
    */
    isHeldDown: {
        type:    Boolean,
        default: false
    }
});

const emit = defineEmits([
    'click'
]);

const isHovered = ref(false);
const isPressed  = ref(false);

const isDown = computed(() => {
    return isPressed.value || props.isHeldDown;
});

function checkHover(e) {
    const bounds = {
                t: 310,
        l: 148,         r: 270,
                b: 370
    };

    isHovered.value = 
        e.offsetX > bounds.l && 
        e.offsetX < bounds.r && 
        e.offsetY > bounds.t && 
        e.offsetY < bounds.b;

    if (!isHovered.value)
        unpress();
}

function checkPress(e) {
    if (isHovered.value && !isDown.value)
        isPressed.value = true;
    else {
        document.activeElement?.blur?.();
        e.preventDefault();
        e.currentTarget.blur();
    }
}

function checkClick(e) {
    e.preventDefault();

    if (isHovered.value) {
        click();
        e.currentTarget.blur();
    }
}

function click() {
    if (!isDown.value)
        emit('click');
}

function unpress() {
    isPressed.value = false;
}
</script>

<style scoped>
.slot-machine-part-button--hovered:not(.slot-machine-part-button--held-down),
.slot-machine-part-button:focus {
    filter: 
        drop-shadow(0  2.75px var(--col-hl-1))
        drop-shadow(0 -2.75px var(--col-hl-1))
        drop-shadow( 2.75px 0 var(--col-hl-1))
        drop-shadow(-2.75px 0 var(--col-hl-1));
}

.slot-machine-part-button--hovered:not(.slot-machine-part-button--held-down) {
    cursor: pointer;
}

.slot-machine-part-button:focus {
    outline: none;
}

.st0{fill:#5933ba;}
.st1{fill:#B20926;}
.st2{fill:#990B29;}
.st3{fill:#770053;}
.st4{fill:#CE295C;}
</style>