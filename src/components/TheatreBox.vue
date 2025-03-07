<template>
	<p>
		TheatreBox
	</p>
	<p>
		x: {{ x }}
	</p>
	<p>
		y: {{ y }}
	</p>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { getProject, types } from '@theatre/core';
import studio from '@theatre/studio';

// Initialize Theatre.js studio (only needed once in your app)
studio.initialize();

// Create a project and sheet
const project = getProject('My Theatre Project');
const sheet = project.sheet('My Scene');

export default defineComponent({
	data() {
		return {
			x: 0,
			y: 0,
		}
	},
	mounted() {
		// Create an object in the sheet
		const obj = sheet.object('Box Position', {
			x: types.number(this.x, { range: [-100, 100] }),
			y: types.number(this.y, { range: [-100, 100] }),
            // c: types.
		});

		// Subscribe to changes
		obj.onValuesChange((values) => {
			this.x = values.x;
			this.y = values.y;
		});
	},
});
</script>

<style scoped>

</style>
