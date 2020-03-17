<template>
  <div class="container">
    <div class="counter">
      <h1>{{counter}}</h1>
    </div>
    <section class="memory-game">
      <div
        v-for="(card, index) in cardDeck"
        class="memory-card"
        :class="[{'disable-card' : cardActivity(index,card)},
        {flip: isFlipped(index,card)}]"
        :key="index"
        :data-framework="card"
        @click="cardFlip(card,index)"
      >
        <img class="front-face" :src="'img/'+card+'.svg'" :alt="card" />
        <img class="back-face" src="img/js-badge.svg" alt="JS Badge" />
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "cards",
  mounted: function() {
    return {
      start: this.start()
    };
  },
  updated: function() {
    this.$nextTick(function() {
      if(this.resetGame){
        // TODO: Better solution for reset ?
        // FIXME: Reset after last card shows.
        this.counter = 30;
        this.shuffle(this.cardDeck);
        this.pairedCards = [];
        this.currentCardIndex = -1;
        this.currentCardName = "";
        this.resetGame = false;
      }
    });
  },
  data: function() {
    return {
      cardNames: ["aurelia", "vue", "angular", "ember", "backbone", "react"],
      currentCardIndex: -1,
      currentCardName: "",
      oldIndexCard: -1,
      indexPair: false,
      namePair: false,
      pairedCards: [],
      counter: 30,
      resetGame: false,
    };
  },
  computed: {
    cardDeck: function() {
      let newDeck = [];

      for (let i = 0; i < this.cardNames.length; i++) {
        newDeck.push(this.cardNames[i]);
        newDeck.push(this.cardNames[i]);
      }
      return this.shuffle(newDeck);
    }
  },
  methods: {
    cardActivity: function(index, card) {
      var isActive =
        this.namePair == true &&
        (this.currentCardIndex == index || this.oldIndexCard == index);
      if (isActive || this.pairedCards.includes(card)) {
        return true;
      }
      return false;
    },
    cardFlip(cardName, index) {
      this.currentCardIndex = index;
      this.currentCardName = cardName;
    },
    isFlipped(index, card) {
      var isFlipped =
        this.currentCardIndex == index ||
        (this.namePair == true && this.oldIndexCard == index) ||
        this.pairedCards.includes(card);
       
      return isFlipped;
    },
    start() {
      this.interval = setInterval(() => {
        this.counter--;
      }, 1000);
    },
    shuffle(array) {
      var currentIndex = array.length,
        temporaryValue,
        randomIndex;

      while (0 !== currentIndex) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex -= 1;

        temporaryValue = array[currentIndex];
        array[currentIndex] = array[randomIndex];
        array[randomIndex] = temporaryValue;
      }

      return array;
    }
  },
  watch: {
    currentCardIndex: function(newIndex, oldIndex) {
      if (
        newIndex != oldIndex &&
        this.currentCardName == this.cardDeck[oldIndex]
      ) {
        this.indexPair = true;
        this.namePair = true;
        this.oldIndexCard = oldIndex;
        this.pairedCards.push(this.currentCardName);
        return;
      }
      this.indexPair = false;
      this.namePair = false;
    },
    counter: function(newValue) {
      if (newValue === -1) {
        if (confirm("You lost the game. Do you want to start a new one?")) {
          this.resetGame = true;
        }
      }
    },
    pairedCards: function(newValue) {
      if(newValue.length == this.cardNames.length){
        if (confirm("You won the game. Do you want to start a new one?")) {
          this.resetGame = true;
        }
      }
    }
  }
};
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
</style>
