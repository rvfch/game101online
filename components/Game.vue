<template lang="html">
  <div class="container">
    <div class="row">
      Cards count: {{ cardsCount }}
    </div>
    <div class="row">
      Time: {{ time }}
    </div>
    <div class="row">
      Which turn: {{ players[turn].playerName }}
    </div>
    <div class="row">
      Current round: {{ round }}
    </div>
    <div class="row">
      <button v-show="!gameStarted" type="button" @click="startGame()">Start game</button>
      <button v-show="gameStarted" type="button" @click="restartGame()">Restart game</button>
      <button v-show="gameStarted" type="button" @click="endGame()">End game</button>
      <button v-show="gameStarted" type="button" @click="pushTurn()">Next turn</button>
      <button v-show="gameStarted" type="button" @click="nextRound()">Next round</button>
    </div>
  </div>
</template>

<style lang="css">
  .container {
    display: flex;
    flex-direction: column;
  }
  .row {
    border-bottom:1px solid #000;
    padding: 5px;
  }
</style>

<script>
import moment from 'moment'
export default {
  data() {
    return {
      // Round count
      round: 1,
      // states viarables
      gameEnded: false,
      gameStarted: false,
      // turn counter
      turn: 0,
      // Timer initializing
      time: '00:00',
      timer: null,
      startTime: 0,
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
      cards: [],
      // Players array of objects
      players: [{
        id: 1,
        playerName: 'Player 1',
        ownCards: [],
        winner: false,
        points: 0
      },
      {
        id: 2,
        playerName: 'Player 2',
        ownCards: [],
        winner: false,
        points: 0
      },
      {
        id: 3,
        playerName: 'Player 3',
        ownCards: [],
        winner: false,
        points: 0
      },
      {
        id: 4,
        playerName: 'Player 4',
        ownCards: [],
        winner: false,
        points: 0
      }
      ]
    }
  },

  mounted() {
    // Calculate count of cards
    this.cardsCount = this.suits.length * this.deck.length

    // debug

  },
  methods: {
    // Function for shuffling cards (Math.random)
    shuffleCards: function (cards) {
      cards = cards.map(a => [Math.random(), a]).sort((a, b) => a[0] - b[0]).map(a => a[1])
      return cards
    },
    // Functions for control a game
    startTimer: function () {
      this.startTime = moment()
      this.timer = setInterval(() => {
        this.time = moment(moment().diff(this.startTime)).format('mm:ss')
      }, 1000)
    },
    stopAndResetTimer: function () {
      clearInterval(this.timer)
      this.startTime = 0
      this.time = '00:00'
    },
    startGame: function () {
      const cardsPerPlayer = this.cardsCount / this.players.length
      this.gameStarted = true
      // set Timer
      this.startTimer()

      // Make deck of cards for every player
      this.cards = this.shuffleCards(this.deck.reduce(function (res, current, index, array) {
        return res.concat([current, current, current, current])
      }, []))

      for (let i = 0; i < this.players.length; i++) {
        for (let j = 0; j < cardsPerPlayer; j++) {
          const randomCard = Math.floor(Math.random() * this.cards.length)
          this.players[i].ownCards.push(this.cards[randomCard])
          this.cards.slice(randomCard, 1)
        }
      }

      console.log(this.players[0].ownCards)
      // Debug
      console.log('Game started')
    },
    endGame: function () {
      this.gameStarted = false
      this.gameEnded = true
      this.turn = 0
      this.round = 1

      this.stopAndResetTimer()
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
    // next round
    nextRound: function () {
      this.turn = 0
      this.round++
      this.stopAndResetTimer()
      this.startTimer()
    },
    // next turn
    pushTurn: function () {
      if (this.turn < this.players.length - 1) {
        this.turn++
      } else {
        this.turn = 0
      }
      // debug
      console.log('push Turn')
    }
  }
}
</script>
