<template>
  <div id="app" class="app">
    <div class="toolbar">
      <button @click="reset" class="btn">
        Reset Game
      </button>
    </div>
    
    <ScoreBoard
    :score="score"
    @reset="reset"
    />
    <section class="grid">
      <Card
        v-for="card in theCards"
        :key="card.id"
        :card="card"
        :cardTransform="cardTransform"
        @tapped="cardTapped"
      />
    </section>
  </div>
</template>

<script>
import Card from './components/Card.vue';
import ScoreBoard from './components/ScoreBoard.vue';

const animals = [
  'hamster',
  'hiyoko',
  'inu',
  'mitsubachi',
  'neko',
  'panda',
  'penguin',
  'usagi'
];

//const shapes = [
//  'triangle',
//  'star',
//  'rhombus',
//  'square',
//  'pentagon',
//  'hexagon',
//  'oval',
//  'circle'
//];

const cards = [];

animals.forEach(item => {
  const card = {
    matchKey: item,
    flipped: false,
    id: `${item}-a`,
    imgUrl: `images/animals/${item}.png`,
    matched: false,
  };

  const cardA = card;
  cards.push(cardA);
  const cardB = { ...card };
  cardB.id = `${item}-b`;
  cards.push(cardB);
});

function shuffle(a) {
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}

function initState() {

  shuffle(cards);

  return {
    totalFlips: 0,
    theCards: cards,
    flipsThisTurn: 0,
    turnCount: 0,
    firstFlipID: null,
    firstFlipMatchKey: null,
    score: [],
    cardTransform: null,
  };
}

export default {
  name: "App",
  components: {
    Card,
    ScoreBoard,
  },

  computed: {
    matchCount() {
      return this.theCards.filter(card => card.matched === true).length / 2;
    },
  },

  methods: {
    incrementFlipsThisTurn() {
      this.flipsThisTurn ++;
    },
    
    cardTapped(tappedCardID) {
      const tappedCard = this.theCards.find(obj => obj.id === tappedCardID);

      if (tappedCard.flipped) {
        return;
      }

      this.incrementFlipsThisTurn();

      if (this.flipsThisTurn === 1) {
        this.runTurn1(tappedCard);
      }
      if (this.flipsThisTurn === 2) {
        this.runTurn2(tappedCard);
      }
    },

    runTurn1(tappedCard) {
      this.flipCard(tappedCard.id);

      this.firstFlipID = tappedCard.id;
      this.firstFlipMatchKey = tappedCard.matchKey;
    },

    runTurn2(tappedCard) {
      this.flipCard(tappedCard.id);
      this.checkMatch(tappedCard);
    },

    checkMatch(tappedCard) {
      setTimeout(() => {
        if (tappedCard.matchKey === this.firstFlipMatchKey) {
          this.flipsThisTurn = 0;

          const newCards = this.theCards.map(card => ([tappedCard.id, this.firstFlipID].includes(card.id)) ? { ...card, matched: true } : card );
          this.theCards = newCards;

          this.score.push('match');
        } else {
          this.flipCard(tappedCard.id);
          this.flipCard(this.firstFlipID);
          this.flipsThisTurn = 0;

          this.score.push('miss');
        }
        this.turnCount ++;
      }, 1000);
    },

    flipCard(tappedCardID) {
      const newCards = this.theCards.map(card => card.id === tappedCardID ? { ...card, flipped: !card.flipped } : card );

      this.theCards = newCards;
    },
    reset() {
      Object.assign(this.$data, initState());
    },
  },
};
</script>

<style>
body {
  background: gray;
  color: red;
  padding: 0;
  margin: 0;
  font-family: arial, helvetica, sans-serif;
  font-size: 15px;
}
.toolbar {
  position: fixed;
  right: 12px;
  top: 15px;
  z-index: 3;
}
.btn {
  appearance: none;
  background: transparent;
  border: 2px solid white;
  color: white;
  font: 11px / 1 arial;
  letter-spacing: 0.3px;
  padding: 4px 12px;
  height: 24px;
  border-radius: 12px;
}
.btn:active {
  color: black;
  background-color: white;
}
.app {
  margin: 0;
  padding: 20px;
}
.grid {
  list-style: none;
  margin: 50px;
  padding: 0;
  display: grid;
  grid-gap: 20px;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  width: calc(100vw - 100px);
  height: calc(100vh - 100px);
}

</style>
