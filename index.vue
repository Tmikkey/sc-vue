<template>
  <div class="main">
    <Block :categories="categories" />
    <div v-if="isModalVisible" class="test"></div>
    <transition name="modalOpen">
      <SeminarForm v-if="isModalVisible" @closePop="closePopUp" />
    </transition>
    <transition name="fade">
      <div v-if="isModalVisible" class="modal-fade" @openPop="fadeOn"></div>
    </transition>
    <div class="container">
      <NoSeminars
        v-if="!futureSeminars.length"
        :style="{ padding: '0 0 0 0 ' }"
        @openPop="openPopUp"
      />
      <div v-if="futureSeminars.length" class="future-link">
        <nuxt-link to="/future_seminars">
          <span> Ближайшие семинары </span>
          <svg
            width="78"
            height="40"
            viewBox="0 0 78 40"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M51.0534 1L76 20M76 20L51.0534 39M76 20H0"
              stroke="#FF4E3D"
              stroke-width="2"
            />
          </svg>
        </nuxt-link>
      </div>
      <ComingSeminar
        v-if="futureSeminars.length"
        :future-seminar-first="lastFutureElements"
      />
      <MainFuture
        v-if="futureSeminars.length"
        :titles="titles"
        :remianing-future-elements="remianingFutureElements"
      />
      <div class="prev-link">
        <nuxt-link to="/past_seminars">
          <span> Прошедшие семинары </span>
          <svg
            width="78"
            height="40"
            viewBox="0 0 78 40"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M51.0534 1L76 20M76 20L51.0534 39M76 20H0"
              stroke="#FF4E3D"
              stroke-width="2"
            />
          </svg>
        </nuxt-link>
      </div>
      <MainLast :titles="titles" :past-seminars="pastSeminars" />
      <SuggestBlock @openPop="openPopUp" />
    </div>
  </div>
</template>

<script>
import Block from '@/components/molecules/Block.vue'
import NoSeminars from 'molecules/NoSeminars'
import ComingSeminar from '@/components/organisms/ComingSeminar.vue'
import MainFuture from '@/components/organisms/MainFuture.vue'
import MainLast from '@/components/organisms/MainLast.vue'
import SeminarForm from 'molecules/SeminarForm.vue'
import SuggestBlock from 'molecules/SuggestBlock.vue'
import { mapGetters } from 'vuex'

export default {
  components: {
    Block,
    NoSeminars,
    ComingSeminar,
    MainFuture,
    MainLast,
    SeminarForm,
    SuggestBlock,
  },
  data: () => {
    return {
      titles: 'Компетенции front-end разработчика / Цели и задачи фронтендера',
      bg: '',
      isModalVisible: false,
      isRowVisible: true,
      checkSeminars: false,
      hideFutureSeminarsElements: true,
      lastFutureElements: {},
      remianingFutureElements: [],
      showPopUpSeminarFormVisible: false,
    }
  },
  async fetch({ store }) {
    const sort = {}
    // if (store.state.seminars.pastSeminars.length === 0) {
    await store.dispatch('seminars/getListPastSeminars')
    await store.dispatch('categories/getCategories')
    // }
    await store.dispatch('seminars/getListFutureSeminars', sort)
  },
  computed: {
    ...mapGetters({
      pastSeminars: 'seminars/PastSeminars',
      futureSeminars: 'seminars/FutureSeminars',
      categories: 'categories/Categories',
    }),
    // getLastFutureElement() {
    //   this.lastFutureElement = this.futureSeminars[
    //     this.futureSeminars.length - 1
    //   ]
    //   console.log(lastFutureElement)
    //   return this.lastFutureElement
    // },
  },
  created() {
    const futureSeminars = JSON.parse(JSON.stringify(this.futureSeminars))
    this.lastFutureElements = futureSeminars.pop()
    this.remianingFutureElements = futureSeminars
  },
  methods: {
    openPopUp() {
      this.isModalVisible = true
      const el = document.body
      el.classList.add('noScroll')
      window.scrollTo(0, 0)
    },
    closePopUp() {
      this.isModalVisible = !this.isModalVisible
      const el = document.body
      el.classList.remove('noScroll')
    },
    hideFutureSeminarsRow() {
      if (this.futureSeminars.length === 1) {
        this.isRowVisible = false
      } else {
        this.isRowVisible = true
      }
    },
    fadeOn() {
      this.showPopUpSeminarFormVisible = true
    },
    // ...mapActions({
    //   getListPastSeminars: 'pastSeminars/getListPastSeminars',
    // }),
  },
}
</script>

<style lang="scss">
.container {
  // margin: 0 auto;
  // min-height: 100vh;
  display: flex;
  justify-content: center;
  flex-flow: wrap;
  max-width: 1280px;
}
.main {
  background-color: #fafbf8;
  margin: 0;
}
header {
  background-color: #fff;
  height: 100px;
}
.future-link {
  width: 100%;
  margin-top: 100px;
  margin-bottom: 80px;
  @media #{$xs-sm} {
    margin-top: 52px;
    margin-bottom: 52px;
  }
  a {
    display: flex;
    color: $RF-dark;
    font-size: 48px;
    font-weight: normal;
    align-items: flex-end;
    @media #{$xs-sm} {
      align-items: end;
      width: 60%;
    }
    span {
      margin-right: 15px;
      font-weight: 500;
      line-height: 54px;
      @media #{$xs-sm} {
        font-size: 28px;
        line-height: 34px;
        max-width: 230px;
      }
      &:hover {
        animation: arrow 0.7s 1 alternate both;
        color: $RF-main;
        @keyframes arrow {
          0% {
            margin-right: 15px;
          }
          50% {
            margin-right: 35px;
          }
        }
      }
    }
    svg {
      min-width: 36px;
    }
  }
}
.prev-link {
  width: 100%;
  margin-top: 100px;
  margin-bottom: 80px;
  a {
    display: flex;
    color: $RF-dark;
    font-size: 48px;
    font-weight: normal;
    align-items: flex-end;
    @media #{$xs-sm} {
      align-items: end;
      width: 60%;
    }
    span {
      margin-right: 15px;
      font-weight: 500;
      line-height: 54px;
      @media #{$xs-sm} {
        font-size: 28px;
        line-height: 34px;
        max-width: 232px;
      }
      &:hover {
        animation: arrow 0.7s 1 alternate both;
        color: $RF-main;
        @keyframes arrow {
          0% {
            margin-right: 15px;
          }
          50% {
            margin-right: 35px;
          }
        }
      }
    }
    svg {
      min-width: 36px;
    }
  }
}
.modal-fade {
  position: fixed;
  z-index: 10;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
  @media #{$xs-sm} {
    top: 59px;
  }
}
.modalOpen-enter-active,
.modalOpen-leave-active {
  transition: all 700ms;
}
.modalOpen-enter,
.modalOpen-leave-to {
  transform: translateX(-2000px);
}
.fade-enter-active {
  animation: 500ms fade forwards;
}
.fade-leave-active {
  animation: 500ms fade reverse;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
@keyframes fade {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>
