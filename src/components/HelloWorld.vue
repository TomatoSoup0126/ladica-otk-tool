<template>
  <div class="flex flex-wrap justify-center">
    <div v-for="card in cards" :key="card.card_id" class="w-1/3 sm:w-1/4 lg:w-1/12 relative">
      <img :src="`https://shadowverse-portal.com/image/card/phase2/common/C/C_${card.card_id}.png?202107030316`">
      <div class="count-panel absolute inset-x-0 bottom-4 text-white rounded-lg w-3/4 mx-auto">
        <div class="flex justify-between opacity-100">
          <div class="ml-1">-</div>
          <h6 class="mx-2 font-bold text-xl">
            {{ handCard[card.card_id] }}
          </h6>
          <div class="mr-1">+</div>
        </div>
        

      </div>
    </div>
  </div>
</template>



<script>
// import { defineProps, reactive } from 'vue'
import { cards } from '../assets/cards.json'
export default {
  data() {
    return {
      cards: cards,
      handCard: {}
    }
  },
  created() {
    this.initHandCard()
  },
  mounted() {
  },
  methods: {
    initHandCard() {
      cards.forEach(card => {
        this.handCard[card.card_id] = 0
      })
    }
  },
  computed: {
    totalHandCardCount() {
      return Object.values(this.handCard).reduce((pv, cv) => pv + Number(cv) , 0)
    },
    remainHandCardCount({totalHandCardCount}) {
      return 9 - totalHandCardCount
    }
  }
}
// defineProps({
// })

// const state = reactive({ 
//   count: 0,
//   handCard: {

//   }
// })
</script>

<style scoped>
img {
  max-width: 100%;
  vertical-align: middle;
}
.count-panel {
  background-color: rgba(156, 163, 175, 0.7);
}
</style>
