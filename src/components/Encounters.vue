<template>
	<h1>Encounters</h1>
	<div class="add-form">
		<div>
			<input type="text" v-model="monsterInput" />
			<!-- <button>Add</button> -->
		</div>
		<div class="monster-list" v-if="filteredMonsters.length > 0">
			<div v-for="monster in filteredMonsters" :key="monster.index">
				{{ monster.name }} <button @click="addMonster(monster)">Add</button>
			</div>
		</div>
		<div v-else-if="filteredMonsters.length < 1 && monsterInput.length < 1">
			<p>
				Search monster
			</p>
		</div>
		<div v-else-if="monsterInput.length > 1 && filteredMonsters.length < 1">
			<input type="number" placeholder="Initiative" v-model="initiativeInput" />
			<input type="number" placeholder="Armor Class" v-model="armorClassInput" />
			<button @click="addCharacter">Add</button>
		</div>
	</div>
	<div class="encounter-group" v-if="filteredEncounterChars.length > 0">
		<h3>Group</h3>
		<Encountered-Item
			v-for="char in filteredEncounterChars"
			:key="char.id"
			:char="char"
			@removeChar="removeCharFromEncounter"
			class="char"
		/>
		<div>
			<button @click="rollDices">Roll</button>
		</div>
	</div>
</template>

<script>
	import EncounteredItem from './EncounteredItem'
	export default {
		data() {
			return {
				charId: 1,
				monsterInput: '',
				monsters: [],
				encounterChars: [],
				initiativeInput: '',
				armorClassInput: ''
			}
		},
		methods: {
			async fetchAllMonsters() {
				await fetch('https://www.dnd5eapi.co/api/monsters')
					.then((res) => res.json())
					.then((res) => {
						res.results.forEach((monstersArr) => {
							this.monsters = this.monsters.concat(monstersArr)
						})
					})
			},
			addCharacter() {
				// if (!this.armorClassInput || !this.initiativeInput) {
				// 	return
				// }
				let id = this.charId
				let characterO = {
					name: this.monsterInput,
					order: null,
					id: id,
					armor_class: this.armorClassInput,
					initiative: this.initiativeInput
				}
				this.encounterChars.push(characterO)
				this.monsterInput = ''
				this.initiativeInput = ''
				this.armorClassInput = ''
				this.charId += 1
			},
			addMonster(monster) {
				let id = this.charId
				let monsterO = {
					name: monster.name,
					armor_class: monster.armor_class,
					order: null,
					id: id,
					index: monster.index,
					initiative: 0
				}
				this.encounterChars.push(monsterO)
				this.monsterInput = ''
				this.charId += 1
			},
			removeCharFromEncounter(char) {
				let index = this.encounterChars.findIndex(
					(monster) => monster.id === char.id
				)
				this.encounterChars.splice(index, 1)
			},
			rollDices() {
				this.encounterChars.forEach((char) => {
					char.order = Math.floor(Math.random() * 20) + 1
					if (char.initiative) {
						char.order +=  parseInt(char.initiative)
					}
				})
			}
		},
		computed: {
			filteredEncounterChars() {
				let hej = this.encounterChars
				hej.sort((a, b) => {
					if (a.order > b.order) {
						return -1
					}
					if (a.order < b.order) {
						return 1
					}
				})
				return hej
			},
			filteredMonsters() {
				return this.monsters.filter((monster) => {
					if (!this.monsterInput) {
						return false
					}
					if (monster.index.includes(this.monsterInput)) {
						return true
					} else {
						return false
					}
				})
			}
		},
		created() {
			this.fetchAllMonsters()
		},
		components: {
			EncounteredItem
		}
	}
</script>

<style scoped>
	.add-form {
		border: 1px solid black;
		padding-top: 10px;
		padding-bottom: 10px;
		display: flex;
		flex-direction: column;
		box-shadow: inset 1px 1px 8px 2px #676767;
		border-radius: 5px;
		height: 250px;
	}
	.monster-list {
		height: 200px;
		overflow-y: auto;
	}
	input {
		border-top: none;
		border-left: none;
		border-right: none;
		border-bottom: 2px solid #676767;
		outline: none;
		margin-right: 5px;
		margin-bottom: 10px;
		height: 20px;
		width: 200px;
	}
	input:focus {
		box-shadow: 1px 1px 1px 1px #676767;
		border-radius: 2px;
	}

	.encounter-group {
		margin-top: 15px;
		border: 1px solid black;
		box-shadow: 1px 7px 10px 3px #676767;
		border-radius: 5px;
		display: flex;
		flex-direction: column;
		padding: 10px;
	}

	.char {
		border: 1px solid black;
		border-radius: 2px;
		margin-bottom: 5px;
		display: flex;
		justify-content: space-around;
		box-shadow: inset 0px 0px 1px 1px #676767;
	}
</style>
