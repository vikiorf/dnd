<template>
	<div>
		<div class="char-order" v-if="char.order">Roll: {{ char.order }}</div>
		<span class="char-name">
			AC:
			{{ monster.armor_class }} | {{ monster.name }}
		</span>

		<img class="image" src="../assets/close.svg" @click="removeChar" />

		<!-- <button @click="removeChar">Remove</button> -->
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
				} else {
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

<style scoped>
.char-order {
	display: flex;
	align-items: flex-start;
	justify-content: flex-start;
}
	.char-name {
		grid-column: 2;
	}
	.image {
		height: 20px;
		width: 20px;
	}
</style>
