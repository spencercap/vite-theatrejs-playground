<template>
	<p>
		HelloWorldSFC
	</p>
	<p>
		{{ bewm }}
	</p>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
	data() {
		return {
			bewm: 'skeet'
		}
	},
	mounted() {
		console.log('mounted');
	},

});
</script>

<style scoped>
.read-the-docs {
	color: #888;
}
</style>
