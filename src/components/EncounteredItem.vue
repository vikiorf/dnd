<template>
	<div>
		Id:{{ char.id }} | Roll: {{ char.order }} | AC: {{ monster.armor_class }} | {{ monster.name }} -
		<button @click="removeChar">Remove</button>
		<!-- <p v-for="(value, name, index) in monster" :key="index">{{ name }} | {{ value }}</p> -->
	</div>
</template>

<script>
	export default {
		emits: ['removeChar'],
		props: ['char'],
		data() {
			return {
				monster: ''
			}
		},
		methods: {
			async fetchMonsterInfo() {
				await fetch('https://www.dnd5eapi.co/api/monsters/' + this.char.index)
					.then((res) => res.json())
					.then((res) => {
						this.monster = res
					})
			},
			removeChar() {
				this.$emit('removeChar', this.char)
			}
		},
		created() {
			this.fetchMonsterInfo()
		}
	}
</script>
