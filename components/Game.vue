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
      <button v-show="gameStarted" type="button" @click="pushTurn(1)">Next turn</button>
      <button v-show="gameStarted" type="button" @click="nextRound()">Next round</button>
    </div>
    <div class="row">
      Remaining cards:
      <div v-for="(card, index) in cards" :key="index" class="card">
        <img :src="generateCardImgUrl(card.name, card.suit)" width="60px">
      </div>
    </div>
    <div class="row">
      Game cards:
      <div v-for="(card, index) in cardsInGame" :key="index" class="card">
        <img :src="generateCardImgUrl(card.name, card.suit)" width="60px">
      </div>
    </div>
    <div class="row">
      {{ players[turn].playerName }} cards:
      <div v-for="(card, index) in players[turn].ownCards" :key="index" class="card">
        <img :class="{selectedCard: card.selected}" :src="generateCardImgUrl(card.name, card.suit)" width="60px" @click="selectCard(card)">
      </div>
    </div>
    <div class="row">
      <button v-show="gameStarted" type="button" @click="putCard()">Put card</button>
      <button v-show="gameStarted" type="button" @click="takeCards(players[turn], 1)">Take card</button>
    </div>
  </div>
</template>

<style lang="css">
  .container {
    display: flex;
    flex-direction: column;
  }
  .row {
    display: flex;
    border-bottom:1px solid #000;
    padding: 5px;
  }

  .card {
    border-radius: 5px;
  }

  .selectedCard {
    border: 2px solid red;
    border-radius: 5px;
  }

  .error {
    color: red;
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
        points: 6,
        suit: ''
      },
      {
        name: '7',
        points: 7,
        suit: ''
      },
      {
        name: '8',
        points: 8,
        suit: ''
      },
      {
        name: '9',
        points: 0,
        suit: ''
      },
      {
        name: '10',
        points: 10,
        suit: ''
      },
      {
        name: 'ace',
        points: 11,
        suit: ''
      },
      {
        name: 'king',
        points: 4,
        suit: ''
      },
      {
        name: 'queen',
        points: 3,
        suit: ''
      },
      {
        name: 'jack',
        points: 2,
        suit: ''
      }
      ],
      // Card suits
      // clubs - крести, diamonds - буби, hearts - червы, spades - пики
      suits: ['clubs', 'diamonds', 'spades', 'hearts'],
      // Count of cards
      cardsCount: 0,
      cards: [],
      cardsInGame: [],
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
      ],

      // game logic // todo!!!!
      relations: []
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
    resetCards: function () {
      this.cards = []
      for (let i = 0; i < this.cardsInGame.length - 1; i++) {
        this.cards.push(this.cardsInGame[i])
        this.cardsInGame.splice(i, 1)
      }
      this.cards = this.shuffleCards(this.cards)
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
      let cardsPerPlayer = 4

      const firstTurnPlayer = Math.floor(Math.random() * this.players.length)
      this.turn = firstTurnPlayer

      this.gameStarted = true
      // set Timer
      this.startTimer()
      // Make deck of cards for every player
      for (let i = 0; i < this.suits.length; i++) {
        for (let j = 0; j < this.deck.length; j++) {
          this.cards.push({
            name: this.deck[j].name,
            points: this.deck[j].points,
            suit: this.suits[i],
            selected: false
          })
        }
      }

      // Shuffle cards
      this.cards = this.shuffleCards(this.cards)

      for (let i = 0; i < this.players.length; i++) {
        if (i === firstTurnPlayer) {
          cardsPerPlayer = 3
        } else {
          cardsPerPlayer = 4
        }
        for (let j = 0; j < cardsPerPlayer; j++) {
          if (this.cards.length > 0) {
            const randomCard = Math.floor(Math.random() * this.cards.length)
            const card = this.cards[randomCard]
            this.cards.splice(randomCard, 1)
            this.players[i].ownCards.push(card)
          }
        }
      }

      // put first card in to game cards
      this.cardsInGame.push(this.cards[this.cards.length - 1])
      this.cards.splice(this.cards.length - 1, 1)

      console.log(this.players[0].ownCards)
      // Debug
      console.log('Game started')
    },
    endGame: function () {
      this.gameStarted = false
      this.gameEnded = true
      this.turn = 0
      this.round = 1
      this.cards = []
      this.cardsInGame = []

      for (let i = 0; i < this.players.length; i++) {
        this.players[i].ownCards = []
      }

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
    pushTurn: function (turns) {
      for (let i = 0; i < turns; i++) {
        if (this.turn < this.players.length - 1) {
          this.turn++
        } else {
          this.turn = 0
        }
      }
      // reset selected cards
      this.players[this.turn].ownCards.forEach(function (ownCard) {
        ownCard.selected = false
      })
      // debug
      console.log('push Turn')
    },
    // take card
    takeCards: function (player, count) {
      if (this.cards.length >= count) {
        for (let i = 0; i < count; i++) {
          player.ownCards.push(this.cards[this.cards.length - i - 1])
          this.cards.splice(this.cards.length - i - 1, 1)
        }
        this.pushTurn(1)
      } else {
        this.resetCards()
      }
    },
    selectCard: function (card) {
      // reset selected cards
      this.players[this.turn].ownCards.forEach(function (ownCard) {
        ownCard.selected = false
      })
      // select card
      card.selected = true
    },
    checkCard: function (card) {
      // logic
      // Check if the suits are same
      if ((card.suit === this.cardsInGame[this.cardsInGame.length - 1].suit) ||
        (card.points === this.cardsInGame[this.cardsInGame.length - 1].points)) {

      } else {
        return 'cardMismatch'
      }
      return true
    },
    putCard: function () {
      let selectedCard
      this.players[this.turn].ownCards.forEach(function (ownCard, index) {
        if (ownCard.selected) {
          selectedCard = ownCard
        }
      })
      if (this.checkCard(selectedCard) === true) {
        console.log(this.turn)
        this.players[this.turn].ownCards.splice(this.players[this.turn].ownCards.indexOf(selectedCard), 1)
        this.cardsInGame.push(selectedCard)
        this.pushTurn(1)
      } else {
        console.log(this.checkCard(selectedCard))
      }
    },
    // helpers Functions
    generateCardImgUrl: function (cardName, suit) {
      return '/cards/' + cardName + '_of_' + suit + '.png'
    }
  }
}
</script>
