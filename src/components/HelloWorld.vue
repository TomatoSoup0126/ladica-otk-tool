<template>
  <h1 class="text-xl mb-4">手牌數量：{{ totalHandCardCount }}</h1>
  <div class="flex flex-wrap justify-center select-none">
    <div v-for="card in cards" :key="card.card_id" class="w-1/3 sm:w-1/4 lg:w-1/12 relative">
      <img :src="`https://shadowverse-portal.com/image/card/phase2/common/C/C_${card.card_id}.png?202107030316`">
      <div class="count-panel absolute inset-x-0 bottom-4 text-white rounded-lg w-3/4 mx-auto">
        <div class="flex justify-between opacity-100">
          <MinusSmIcon
            :class="{'cursor-pointer': handCard[card.card_id] > 0, 'cursor-not-allowed':  handCard[card.card_id] === 0}"
            @click="handCard[card.card_id] > 0 && handleCardMinus(card.card_id)"/>
          <h6 class="mx-2 font-bold text-xl cursor-default" :class="{'text-yellow-400': handCard[card.card_id] === 3}" >
            {{ handCard[card.card_id] }}
          </h6>
          <PlusIcon
            :class="{ 'cursor-pointer': handCard[card.card_id] < 3 , 'cursor-not-allowed':  handCard[card.card_id] === 3 || remainHandCardCount === 0}"
            @click="handCard[card.card_id] < 3 && remainHandCardCount > 0 && handleCardPlus(card.card_id)"/>
        </div>
        

      </div>
    </div>
  </div>
</template>



<script>
import { cards } from '../assets/cards.json'
import { PlusIcon } from '@heroicons/vue/solid'
import { MinusSmIcon } from '@heroicons/vue/solid'

export default {
  data() {
    return {
      cards: cards,
      handCard: {}
    }
  },
  components: {
    PlusIcon,
    MinusSmIcon
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
    },
    handleCardMinus(id) {
      this.handCard[id]--
    },
    handleCardPlus(id) {
      if (this.handCard[id] < 3 ) this.handCard[id]++
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
