<template lang="html">
  <div>
    cardsCount: {{ cardsCount }}
    Time: {{ timer }}
    Turn: {{ turn }}
    <button v-show="!gameStarted" type="button" @click="startGame()">Start game</button>
    <button type="button" @click="restartGame()">Restart game</button>
    <button type="button" @click="endGame()">End game</button>
    <button type="button" @click="pushTurn()">Next turn</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // states viarables
      gameEnded: false,
      gameStarted: false,
      // player id each turn (Player 1 -> turn = 1 ; Player 2 -> turn = 2 ; etc.)
      turn: 0,
      // Timer initializing
      timer: '--:--',
      // count of players (only for testing)
      playersCount: 4,
      // Default card deck and points for each card
      deck: [{
        name: '6',
        points: 6
      },
      {
        name: '7',
        points: 7
      },
      {
        name: '8',
        points: 8
      },
      {
        name: '9',
        points: 0
      },
      {
        name: '10',
        points: 10
      },
      {
        name: 'ace',
        points: 11
      },
      {
        name: 'king',
        points: 4
      },
      {
        name: 'queen',
        points: 3
      },
      {
        name: 'jack',
        points: 2
      }
      ],
      // Card suits
      // clubs - крести, diamonds - буби, hearts - червы, spades - пики
      suits: ['clubs', 'diamonds', 'spades', 'hearts'],
      // Count of cards
      cardsCount: 0,
      // Players array of objects
      players: [
        {
          id: 1,
          playerName: 'Player 1',
          ownCards: [],
          winner: false
        },
        {
          id: 2,
          playerName: 'Player 2',
          ownCards: [],
          winner: false
        },
        {
          id: 3,
          playerName: 'Player 3',
          ownCards: [],
          winner: false
        },
        {
          id: 4,
          playerName: 'Player 4',
          ownCards: [],
          winner: false
        }
      ]
    }
  },

  mounted() {
    // Calculate count of cards
    this.cardsCount = this.suits.length * this.deck.length

    // debug
    console.log(this.players)
  },
  methods: {
    // Function for shuffling cards (Math.random)
    shuffleCards: function () {

    },
    // Functions for control a game
    startGame: function () {
      this.gameStarted = true
      this.pushTurn()
      // Debug
      console.log('Game started')
    },
    endGame: function () {
      this.gameStarted = false
      this.gameEnded = true
      this.turn = 0
      // debug
      console.log('Game ended')
    },
    restartGame: function () {
      this.endGame()
      this.gameEnded = false
      this.startGame()
      // debug
      console.log('Game restarted')
    },
    // Functions for game logic
    pushTurn: function () {
      if (this.turn < this.players.length) { this.turn++ } else if (!this.gameEnded) { this.turn = 1 } else { this.turn = 0 }
      // debug
      console.log('push Turn')
    }
  }
}
</script>

<style lang="css">
</style>
