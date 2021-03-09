<template>
	<h1>Encounters</h1>
	<input type="text" v-model="monsterInput"/>
	<button>Add</button>
  <!-- <div v-for="monster in monsters" :key="monster.index">{{ monster }}</div> -->
  <div v-for="monster in filteredMonsters" :key="monster.index">{{ monster }}</div>
</template>

<script>
	export default {
		data() {
			return {
        monsterInput: '',
				monsters: []
			}
		},
		methods: {
			async fetchAllMonsters() {
				await fetch('https://www.dnd5eapi.co/api/monsters')
					.then((res) => res.json())
					.then((res) => {
            res.results.forEach(monstersArr => {
              this.monsters = this.monsters.concat(monstersArr)
            })
            })
			}
		},
    computed: {
      filteredMonsters() {
        return this.monsters.filter(monster => {
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
    }
	}
</script>
