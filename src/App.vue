<template lang="pug">
	div
		bstage
</template>

<script>
	import bstage from '@/components/bstage.vue';
	export default {
		name: 'app',
		components:{
			'bstage': bstage,
		},
		mounted(){
			// window.addEventListener('DOMContentLoaded', function() {
		        // your code here
		    // });
		},
	}
</script>

<style lang="sass">
	html, body
		overflow: hidden
		width: 100%
		height: 100%
		margin: 0
		padding: 0
	div
		width: 100%
		height: 100%
</style>
