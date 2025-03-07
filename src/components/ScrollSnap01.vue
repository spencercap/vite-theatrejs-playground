<template>
    <div>
        <h2>ScrollSnap01 [opt + \] - scroll: {{ scrollOffset.toFixed(2) }}, sequence: {{ sequencePosition.toFixed(2) }} </h2>
        <button class="snap-toggle" @click="toggleSnapMode">
            Snap Mode: {{ snapMode }}
        </button>
        <div class="box" :style="{ transform: `translate(${x}px, ${y}px)` }"></div>

        <p>some stuff before</p>

        <div class="scroll-container">
            <div 
                class="scroll-content" 
                ref="scrollContent" 
                @scroll="onScroll"
                :style="{ 'scroll-snap-type': `y ${snapMode}` }"
            >
                <div class="scroll-item">Item 1</div>
                <div class="scroll-item">Item 2</div>
                <div class="scroll-item">Item 3</div>
            </div>
        </div>

        <p>some stuff after</p>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { getProject, types } from '@theatre/core';
import studio from '@theatre/studio';
import theatreState from '../assets/theatre-state.json';

// Initialize Theatre.js studio (only needed once in your app)
studio.initialize();

// Create a project and sheet
const project = getProject('My Theatre Project', { state: theatreState });
const sheet = project.sheet('My Scene');

// Create a sequence with a specific duration (e.g., 3 seconds)
// const sequence = sheet.sequence.attachAudio().createBlankTrack();
project.ready.then(() => {
    sheet.sequence.play({ iterationCount: 1, range: [0, 3] });
});


export default defineComponent({
	data() {
		return {
			x: 0,
			y: 0,
			sequenceLength: 3, // Set a fixed duration for now
			sequencePosition: 0,
			scrollOffset: 0,
			snapMode: 'proximity' as 'proximity' | 'mandatory'
		}
	},
	methods: {
		toggleSnapMode() {
			this.snapMode = this.snapMode === 'proximity' ? 'mandatory' : 'proximity';
		},
		onScroll(event: Event) {
			const el = event.target as HTMLElement;
			this.scrollOffset = el.scrollTop / (el.scrollHeight - el.clientHeight);
		},
		updateFrame() {
			this.sequencePosition = this.scrollOffset * this.sequenceLength;
			sheet.sequence.position = this.sequencePosition;
			requestAnimationFrame(this.updateFrame);
		}
	},
	mounted() {
		// Create an object in the sheet
		const obj = sheet.object('Box Position', {
			x: types.number(this.x, { range: [0, 800] }),
			y: types.number(this.y, { range: [0, 800] }),
		});

		// Subscribe to changes
		obj.onValuesChange((values) => {
			this.x = values.x;
			this.y = values.y;
		});

		// Initialize sequence control
		this.updateFrame();
	}
});
</script>

<style scoped>
h2 {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 1;
}

.scroll-container {
    /* position: fixed; */
    top: 0;
    left: 0;
    width: 100%;
    min-width: 300px;
    height: 100vh;
    overflow: hidden;
}

.scroll-content {
    height: 100%;
    display: flex;
    flex-direction: column;
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
}   

.scroll-item {
    width: 100%;
    height: 100%;
    scroll-snap-align: start;
    scroll-snap-stop: always;
    flex-shrink: 0;
    background-color: red;
}   

.scroll-item:nth-child(2) {
    background-color: blue;
}

.scroll-item:nth-child(3) {
    background-color: green;
}

.box {
	position: fixed;
	top: 80px;
	left: 0%;
	width: 100px;
	height: 100px;
	background-color: lime;
    z-index: 10;
}

.snap-toggle {
    position: fixed;
    top: 40px;
    right: 0;
    z-index: 1;
    padding: 8px 16px;
    background-color: #333;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.snap-toggle:hover {
    background-color: #444;
}
</style>
