<template>
  <div class="text-xl mb-4 select-none">
    <h1>手牌數量：{{ totalHandCardCount }}</h1>
    <h1 class="mt-4">
      <input type="checkbox" v-model="hasEvoPoint" id="evoPoint">
      <label for="evoPoint" class="ml-2">尚有進化點</label>
    </h1>
  </div>
  <div class="flex flex-wrap justify-center select-none">
    <div v-for="card in cards" :key="card.card_id" class="w-1/3 sm:w-1/4 lg:w-1/12 relative">
      <img :src="`https://shadowverse-portal.com/image/card/phase2/common/C/C_${card.card_id}.png?202107030316`">
      <div class="count-panel absolute inset-x-0 bottom-4 text-white rounded-lg w-3/4 mx-auto">
        <div class="flex justify-between opacity-100">
          <MinusSmIcon
            :class="{'cursor-pointer': handCard[card.card_id] > 0, 'cursor-not-allowed': handCard[card.card_id] === 0}"
            @click="handCard[card.card_id] > 0 && handleCardMinus(card.card_id)"/>
          <h6 class="mx-2 font-bold text-xl cursor-default" :class="{'text-yellow-400': handCard[card.card_id] === 3}">
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
      hasEvoPoint: true,
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
    },
    zeroTokenSum() {
      let sum = 0
      let fairyWisp = this.handCard['900111020'] // 妖精螢火
      let youngCat = this.handCard['900131040'] // 年輕又自由的貓
      let windFairy = this.handCard['119111040'] // 迅風妖精
      let deepwoodWolf = this.handCard['117111030'] // 森林之狼
      let cryptidKeeper = this.handCard['121121020'] // 幻獸保衛者


      sum += this.handCard['900111020'] // 妖精螢火
      sum += this.handCard['900131040'] // 年輕又自由的貓
      sum += this.handCard['900134050'] // 拉緹卡的擁抱
      sum += this.handCard['121134010'] // 英雄的覺悟
      sum += this.handCard['121141020'] // 萬綠的回歸‧拉緹卡

      if (fairyWisp > 0 && windFairy > 0) { // 妖精螢火組合迅風妖精算2
        const discount = fairyWisp >= windFairy ? windFairy : fairyWisp
        fairyWisp -= discount
        windFairy -= discount
        sum += discount * 2
      }

      if (youngCat > 0 && windFairy > 0) { // 年輕又自由的貓組合迅風妖精算2
        const discount = youngCat >= windFairy ? windFairy : youngCat
        youngCat -= discount
        windFairy -= discount
        sum += discount * 2
      }

      if (fairyWisp > 0 && deepwoodWolf > 0) { // 妖精螢火組合森林之狼算2
        const discount = fairyWisp >= deepwoodWolf ? deepwoodWolf : fairyWisp
        fairyWisp -= discount
        deepwoodWolf -= discount
        sum += discount
      }

      if (youngCat > 0 && deepwoodWolf > 0) { // 年輕又自由的貓組合森林之狼算2
        const discount = youngCat >= deepwoodWolf ? deepwoodWolf : youngCat
        youngCat -= discount
        deepwoodWolf -= discount
        sum += discount
      }

      if (cryptidKeeper > 0 && this.hasEvoPoint) { // 幻獸保衛者有進化點或回手才納入計算
        cryptidKeeper--
        sum++
      }

      if (cryptidKeeper > 0 && windFairy > 0) { // 還有回手方法的話幻獸保衛者算1
        const discount = cryptidKeeper >= windFairy ? windFairy : cryptidKeeper
        cryptidKeeper -= discount
        windFairy -= discount
        sum += discount
      }

      if (cryptidKeeper > 0 && deepwoodWolf > 0) { // 還有回手方法的話幻獸保衛者算1
        const discount = cryptidKeeper >= deepwoodWolf ? deepwoodWolf : cryptidKeeper
        cryptidKeeper -= discount
        deepwoodWolf -= discount
        sum += discount
      }
      
      return sum
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
