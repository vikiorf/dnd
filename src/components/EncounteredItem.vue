<template>
	<div>
		<span v-if="char.order">Roll: {{ char.order }}</span>
		AC:
		{{ monster.armor_class }} | {{ monster.name }}
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
				if (this.char.index) {
					await fetch('https://www.dnd5eapi.co/api/monsters/' + this.char.index)
						.then((res) => res.json())
						.then((res) => {
							this.monster = res
						})
				} else  {
					this.monster = this.char
				}
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
