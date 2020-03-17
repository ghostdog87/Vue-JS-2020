<template>
  <div class="container">
    <div class="counter">
      <h1>{{counter}}</h1>
    </div>
    <section class="memory-game">
      <div
        v-for="(card, index) in cardDeck"
        class="memory-card"
        @click="cardFlip(card,index)"
        :class="[{'disable-card' : cardActivity(index,card)},
        {flip: isFlipped(index,card)}]"
        :key="index"
        :data-framework="card"
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
      if (this.isGameOver) {
        // TODO: Better solution to reset game/component?
        // TODO: Reset after last card shows up.
        this.counter = 30;
        this.shuffle(this.cardDeck);
        this.pairedCards = [];
        this.currentCardIndex = -1;
        this.currentCardName = "";
        this.isGameOver = false;
      }
    });
  },
  data: function() {
    return {
      cardNames: ["aurelia", "vue", "angular", "ember", "backbone", "react"],
      currentCardIndex: -1,
      currentCardName: "",
      oldCardIndex: -1,
      pairedCards: [],
      counter: 30,
      isNamePair: false,
      isGameOver: false
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
    cardActivity(index, card) {
      var isActive =
        this.isNamePair == true &&
        (this.currentCardIndex == index || this.oldCardIndex == index);
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
        (this.isNamePair == true && this.oldCardIndex == index) ||
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
        this.isNamePair = true;
        this.oldCardIndex = oldIndex;
        this.pairedCards.push(this.currentCardName);
        return;
      }
      this.isNamePair = false;
    },
    counter: function(newValue) {
      if (newValue == -1) {
        if (confirm("You lost the game. Do you want to start a new one?")) {
          this.isGameOver = true;
        }
      }
    },
    pairedCards: function(newValue) {
      if (newValue.length == this.cardNames.length) {
        if (confirm("You won the game. Do you want to start a new one?")) {
          this.isGameOver = true;
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
