<template>
    <div class="game">
      <div v-if="playing">
        <div class="game-field">
          <div class="column">
            <h1>You: {{playerHealth}}</h1>
          </div>
          <div class="column">
            <h1 @click="selectSkill1" class="skill">Deal [number] x 10 damage</h1>
            <h1 @click="selectSkill2" class="skill">Heal [number] x 5 health</h1>
          </div>
          <div class="column">
            <h1>Opponent: {{enemyHealth}}</h1>
          </div>
        </div>
        <div class="game-dice">
          <button v-for="(die, index) in dice" :key="die.id" class="dice" @click="selectDice">
            <p>{{die.number}}</p>
            <p class="hide">{{index}}</p>
          </button>
        </div>
      </div>
      <div v-else class="end-screen">
        <h1 v-if="won" class="result">You win!</h1>
        <h1 v-else class="result">You lose!</h1>
        <button @click="restartGame" class="retry">Retry</button>
      </div>
    </div>
</template>

<script>
export default {
   data() {
    return {
      dice: [],
      targetedDice: null,
      diceNumber: null,
      indexNumber: null,
      diceSelected: false,
      skillSelected: false,
      damageNumber: null,
      healNumber: null,
      playerHealth: 100,
      enemyHealth: 400,
      playing: true,
      won: null,
    }
  },
  methods: {
    selectDice(e) {
      //make sure die button is selected
      if (e.target.tagName == 'BUTTON') {
        this.targetedDice = e.target
      } else {
        this.targetedDice = e.target.parentElement
      }
      //record number displayed on die, die index number
        this.diceNumber = this.targetedDice.innerText
        this.indexNumber = this.targetedDice.lastElementChild.innerHTML
        this.diceSelected = true
    },
    selectSkill1() {
      //dice number is multiplied by 10, deducted from opponent, and die is removed from dice array
      if (this.diceSelected == true) {
        const usedDice = this.dice[this.indexNumber]
        this.dice = this.dice.filter((d) => d !== usedDice)
        this.damageNumber = this.diceNumber * 10
        this.enemyHealth = this.enemyHealth - this.damageNumber
      }
    },
    selectSkill2() {
      //dice number is multiplied by 5, added to user, and die is removed from array
      if (this.diceSelected == true) {
        const usedDice = this.dice[this.indexNumber]
        this.dice = this.dice.filter((d) => d !== usedDice)
        this.healNumber = this.diceNumber * 5
        this.playerHealth = this.playerHealth + this.healNumber
      }
    },
    restartGame() {
      //reset everything
      this.playing = true
      this.playerHealth = 100
      this.enemyHealth = 300
    }
  },
  mounted() {
    //generates 4 random dice upon first entering the site
    for (let i = 0; i < 4; i++) {
    let randomNumber = Math.floor(Math.random()*6)+1
    this.dice.push({number: randomNumber})
    }
  },
  updated() {
    //when only 2 dice are left, they are removed
    if (this.dice.length == 2) {
      this.dice.length = 0
      //player takes a random amount of damage between 30-40
      let takenDamage = Math.floor(Math.random()*30)+10
      this.playerHealth = this.playerHealth - takenDamage
      //generates 4 new dice
      for (let i = 0; i < 4; i++) {
        let randomNumber = Math.floor(Math.random()*6)+1
        this.dice.push({number: randomNumber})
      }
    }
    //if opponent health reaches 0, display win screen and retry button
    if (this.enemyHealth <= 0) {
      this.playing = false
      this.won = true
      //if player health reaches 0, display lose screen and retry button
    } else if (this.playerHealth <= 0) {
      this.playing = false
      this.won = false
    }
  }
}
</script>

<style scoped>
.game {
  margin: 5rem;
}

.game-field {
  display: flex;
  justify-content: space-between;
  box-sizing: border-box;
}

.column {
  width: 30vw;
  display: flex;
  flex-direction: column;
  text-align: center;
  justify-content: center;
}

.skill {
  border: 0.3rem solid black;
  border-radius: 20px;
  padding: 1rem;
}

.skill:hover {
  background: rgb(197, 197, 197); 
}

.game-dice {
  display: flex;
  width: 30vw;
  justify-content: center;
}

.dice {
  border: 0.3rem solid black;
  border-radius: 20px;
  width: 5vw;
  aspect-ratio: 1 / 1;
  font-size: 2rem;
  text-align: center;
  margin: 1rem;
  display: flex;
  align-items: center;
  justify-content: center;
}

.dice:hover {
  background: rgb(197, 197, 197);
}

.dice:focus {
  border: 0.3rem solid blue;
}

.end-screen {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.result {
  font-size: 5rem;
  text-align: center;
}

.retry {
  border: 0.3rem solid black;
  border-radius: 20px;
  width: 20vw;
  font-size: 2rem;
  padding: 1rem;
}

.retry:hover {
  background: rgb(197, 197, 197);
}

.hide {
  display: none;
}

p {
  margin: 0;
}

</style>