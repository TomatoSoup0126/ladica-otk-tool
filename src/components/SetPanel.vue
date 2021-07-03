<template>
  <div class="text-xl mb-6 select-none text-white">
    <div class="sm:flex sm:flex-wrap mb-4">
      <div class="relative cursor-pointer mb-2 sm:mb-0" @click="setPlayedTarget(11)">
        <CheckCircleIcon v-show="playedTarget === 11" class="check-icon text-white h-5 w-5 absolute inset-y-0 my-auto" />
        <img src="https://shadowverse-portal.com/image/card/phase2/common/L/L_104141020.jpg?202107030308">
      </div>
      <div class="relative cursor-pointer mb-2 sm:mb-0" @click="setPlayedTarget(12)">
        <CheckCircleIcon v-show="playedTarget === 12" class="check-icon text-white h-5 w-5 absolute inset-y-0 my-auto" />
        <img src="https://shadowverse-portal.com/image/card/phase2/common/L/L_121141020.jpg?202107030308">
      </div>
    </div>
    <div class="sm:flex sm:flex-wrap px-4 mb-4">
      <div class="my-auto">
        <label for="playPoints" class="mr-2">PP</label>
        <select v-model="playPoints" id="playPoints" class="bg-black">
          <option
            v-for="number in 10"
            :key="`pp_${number}`" 
            :value="number">
            {{ number }}
          </option>
        </select>
      </div>
      <p class="px-2 my-auto hidden sm:block">|</p>
      <div class="ml-2 my-auto">
        <input type="checkbox" v-model="hasEvoPoint" id="evoPoint">
        <label for="evoPoint" class="ml-2">EP</label>
      </div>
      <p class="px-2 mr-2 my-auto hidden sm:block">|</p>
      <RefreshIcon class="text-white h-5 w-5 my-auto cursor-pointer" @click="resetHandCards"/>
    </div>
  </div>
  <hr class="mt-4 mb-8">
  <div class="flex flex-wrap select-none">
    <div v-for="card in cards" :key="card.card_id" class="w-1/3 sm:w-1/6 relative">
      <img :src="`https://shadowverse-portal.com/image/card/phase2/common/C/C_${card.card_id}.png?202107030316`">
      <div class="count-panel absolute inset-x-0 bottom-4 text-white rounded-lg w-3/4 mx-auto">
        <div class="flex justify-between opacity-100">
          <MinusSmIcon
            :class="{'cursor-pointer': handCard[card.card_id] > 0, 'cursor-not-allowed': handCard[card.card_id] === 0}"
            @click="handCard[card.card_id] > 0 && handleCardMinus(card.card_id)"/>
          <h6 class="mx-2 font-bold text-3xl cursor-default align-middle" :class="{'text-yellow-400': handCard[card.card_id] > 0}">
            {{ handCard[card.card_id] }}
          </h6>
          <PlusIcon
            class="cursor-pointer"
            :class="{'cursor-not-allowed': remainHandCardCount === 0}"
            @click="remainHandCardCount > 0 && handleCardPlus(card.card_id)"/>
        </div>
      </div>
    </div>
    <div class="flex flex-grow w-16 h-16 text-white my-auto justify-center">
      <CollectionIcon class="w-5 h-5"/>
      <span class="ml-2" :class="{'text-red-500': totalHandCardCount === 9}">x {{ totalHandCardCount }}</span>
    </div>
    <div class="flex flex-grow w-16 h-16 text-white my-auto justify-center">
      <UploadIcon class="w-5 h-5"/>
      <span class="mx-2">
        x {{ playedTarget === 12 ? 12 : 8 }}
      </span>
      <XCircleIcon v-show="!isCanPlayedWithTarget" class="w-5 h-5 text-red-500"/>
      <CheckCircleIcon v-show="isCanPlayedWithTarget" class="w-5 h-5 text-green-500"/>
    </div>
  </div>
</template>



<script>
import { cards } from '../assets/cards.json'
import { PlusIcon, MinusSmIcon, CheckCircleIcon, CollectionIcon, UploadIcon, XCircleIcon, RefreshIcon } from '@heroicons/vue/solid'

export default {
  data() {
    return {
      cards: cards,
      hasEvoPoint: true,
      handCard: {},
      playPoints: 6,
      playedTarget: 12
    }
  },
  components: {
    PlusIcon,
    MinusSmIcon,
    CheckCircleIcon,
    CollectionIcon,
    UploadIcon,
    XCircleIcon,
    RefreshIcon
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
      this.handCard[id]++
    },
    setPlayedTarget(target) {
      this.playedTarget = target
    },
    resetHandCards() {
      this.handCard = this.$options.data().handCard
      this.initHandCard()
    }
  },
  computed: {
    totalHandCardCount() {
      return Object.values(this.handCard).reduce((pv, cv) => pv + Number(cv) , 0)
    },
    remainHandCardCount({totalHandCardCount}) {
      return 9 - totalHandCardCount
    },
    zeroCostTokenSum() {
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
    },
    oneCostTokenSum() {
      let sum = 0
      this.countOneList.forEach(cardId => { sum += this.handCard[cardId]})
      this.countTwoList.forEach(cardId => { sum += this.handCard[cardId] * 2})
      this.countThreeList.forEach(cardId => { sum += this.handCard[cardId] * 3})
      return sum
    },
    countOneList() {
      return [
        '900111010', // 妖精
        '119121010', // 森林遊俠‧維爾達
        '121134010', // 英雄的覺悟
        '121141020', // 萬綠的回歸‧拉緹卡
        '117111030', // 森林之狼
        '119111040', // 迅風妖精
        '900134010', // 暹羅的惡作劇
        '900134020' // 暹瑪的凝視
      ]
    },
    countTwoList() {
      return [
        '120141020' // 宿命的狐火‧雪華
      ]
    },
    countThreeList() {
      return [
        '118141010', // 優美的貓姊妹‧暹羅與暹瑪
        '120141030' // 龐然妖花
      ]
    },
    isCanPlayedWithTarget() {
      if (this.playedTarget === 12 && this.handCard['121141020'] === 0) return false // 沒拉緹卡
      if (this.playedTarget === 11 && this.handCard['117134010'] === 0) return false // 沒生命之宴
      if (this.zeroCostTokenSum === this.playedTarget ) { // 0費牌滿足條件
        return true
      }

      return (this.playedTarget - this.playPoints) < this.zeroCostTokenSum && (this.playPoints - this.oneCostTokenSum) < 0
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
.check-icon {
  left: 2rem
}
</style>
