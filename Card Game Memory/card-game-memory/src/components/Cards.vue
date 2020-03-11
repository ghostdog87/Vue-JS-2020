<template>
  <div class="container">
    <div class="counter">
      <h1>60</h1>
    </div>
    <section class="memory-game">
      <div
        v-for="(card, index) in cardDeck"
        class="memory-card"
        :class="[{'disable-card':namePair == true && (currentCardIndex == index || oldIndexCard == index)},
        {flip: currentCardIndex == index || (namePair == true && oldIndexCard == index)}]"
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
  data: function() {
    return {
      cardNames: ["aurelia", "vue", "angular", "ember", "backbone", "react"],
      currentCardIndex: -1,
      currentCardName: "",
      oldIndexCard: -1,
      indexPair: false,
      namePair: false
    };
  },
  computed: {
    cardDeck: function() {
      let newDeck = new Array();

      for (let i = 0; i < this.cardNames.length; i++) {
        newDeck.push(this.cardNames[i]);
        newDeck.push(this.cardNames[i]);
      }

      return this.shuffle(newDeck);
    },
    cardActivity: function() {
      return this.isPair() == false ? "" : "disable-card";
    }
    
  },
  methods: {
    isPair: function() {
      if (this.indexPair == false && this.namePair == true){
        return true;
      }
      return false;
    },
    cardFlip(cardName, index) {
      this.currentCardIndex = index;
      this.currentCardName = cardName;
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
      if(newIndex != oldIndex && this.currentCardName == this.cardDeck[oldIndex]){
        this.indexPair = true;
        this.namePair = true;
        this.oldIndexCard = oldIndex;
        this.cardActivity();
        return;
      }
      this.indexPair = false;
      this.namePair = false;
    } 
  }
};
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
</style>
