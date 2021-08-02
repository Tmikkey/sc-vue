<template>
  <div class="container">
    <div class="content-future">
      <Backward />
      <div class="comming"><span>Ближайшие</span> cеминары</div>
      <ComingSeminar :future-seminar-first="lastFutureElements" />
      <FilterModal
        v-if="isFilterMobile"
        :month="month"
        :formats="formats"
        :categories="categories"
        :selected-format="selectedFormat"
        :selected-categorie="selectedCategorie"
        :selected-month="selectedMonth"
        @select="selectOption"
        @closePop="closePopUp"
      />
      <div class="filter">
        <TheFilter
          :month="month"
          :formats="formats"
          :categories="categories"
          :selected-format="selectedFormat"
          :selected-categorie="selectedCategorie"
          :selected-month="selectedMonth"
          @select="selectOption"
        />
      </div>
      <div class="filter-mobile">
        <div
          class="filter-mobile__inner"
          @click=";(isFilterMobile = true), toggleBodyClass()"
        >
          <div class="lines">
            <div class="lines lines_1"></div>
            <div class="lines lines_2"></div>
            <div class="lines lines_3"></div>
          </div>
          <div class="text-filters">фильтры</div>
        </div>
      </div>
      <ul>
        <SeminarPreview
          v-for="item in futureSeminarsFull"
          :key="item.id"
          :titles="titles"
          :future-seminars="item"
        />
      </ul>
    </div>
  </div>
</template>

<script>
import SeminarPreview from '@/components/molecules/SeminarPreview.vue'
import TheFilter from '@/components/organisms/TheFilter.vue'
import ComingSeminar from '@/components/organisms/ComingSeminar.vue'
import FilterModal from 'organisms/FilterModal.vue'
import Backward from 'atoms/Backward.vue'
import { mapActions, mapGetters } from 'vuex'

export default {
  name: 'FutureSeminars',
  components: {
    Backward,
    ComingSeminar,
    FilterModal,
    SeminarPreview,
    TheFilter,
  },
  data: () => {
    return {
      titles: 'Коллективный Brainstorm | Workshop',
      selectedFormat: 'Все форматы',
      selectedCategorie: 'Все категории',
      selectedMonth: 'Месяц',
      isFilterMobile: false,
      lastFutureElements: {},
      remianingFutureElements: [],

      // prettier-ignore
      arrayTest: [
        {id:1,},
        {id:2,},
        {id:3,},
        {id:4,},
        {id:5,},
        {id:6,},
        {id:7,},
        {id:8,},
      ],
      // prettier-ignore
      month: [
        // {name: "Месяц",},
        { name: "Месяц",
          id: 0,
        },
        { name: "Январь",
          id: 1,
        },
        { name: "Февраль",
          id:2,
        },
        { name: "Март",
          id:3,
        },
        { name: "Апрель",
          id:4,
        },
        { name: "Май",
          id:5,
        },
        { name: "Июнь",
          id:6,
        },
        { name: "Июль",
          id:7,
        },
        { name: "Август",
          id:8,
        },
        { name: "Сентябрь",
          id:9,
        },
        { name: "Октябрь",
          id:10,
        },
        { name: "Ноябрь",
          id:11,
        },
        { name: "Декабрь",
          id:12,
        },
      ],
      // prettier-ignore
      // formats: [
      // {name: "Все форматы",},
      //   {name: "онлайн",},
      //   {name: "оффлайн",},
      // ],
      // prettier-ignore
      // categories: [
      // {name: "Все категории",},
      // {name: "маркетинг",},
      // {name: "управление",},
      // {name: "дизайн",},
      // {name: "соцсети",},
      // {name: "HR",},
      // {name: "back-end",},
      // {name: "front-end",},
      // {name: "бизнес",},
      // ],
      classTest: 'active5',
      // prettier-ignore
      styles: [
        {class: "1",},
        {class: "2",},
        {class: "3",},
        {class: "4",},
        {class: "5",},
        {class: "6",},
        {class: "7",},
      ],
      format: 'Все форматы',
    }
  },
  async fetch({ store }) {
    const sort = {}
    await store.dispatch(`seminars/getListFutureSeminarsFull`, sort)

    if (store.state.categories.categories.length === 0) {
      await store.dispatch('categories/getCategories')
    }
    if (store.state.formats.formats.length === 0) {
      await store.dispatch('formats/getFormats')
    }
  },
  created() {
    const futureSeminarsFull = JSON.parse(
      JSON.stringify(this.futureSeminarsFull)
    )
    this.lastFutureElements = futureSeminarsFull.pop()
    this.remianingFutureElements = futureSeminarsFull
  },
  computed: {
    ...mapGetters({
      futureSeminarsFull: 'seminars/FutureSeminarsFull',
      categories: 'categories/Categories',
      formats: 'formats/Formats',
    }),
  },
  methods: {
    selectOption(filterData) {
      this.selectedFormat = filterData.format.title
      this.selectedCategorie = filterData.category.title
      this.selectedMonth = filterData.month.name
      console.log(filterData)
      this.getListFutureSeminarsFull({
        format: filterData.format.id,
        category: filterData.category.id,
        month: filterData.month.id,
      })
    },
    // selectOptionCategorie(item) {
    //   this.selectedCategorie = item.name
    // },
    // selectOptionMonth(item) {
    //   this.selectedMonth = item.name
    // },
    closePopUp() {
      this.isFilterMobile = !this.isFilterMobile
      const el = document.body
      el.classList.remove('noScroll')
    },
    toggleBodyClass() {
      const el = document.body
      window.scrollTo(0, 0)
      el.classList.add('noScroll')
    },
    ...mapActions({
      // getCategories: 'categories/getCategories',
      // getFormats: 'formats/getFormats',
      getListFutureSeminarsFull: 'seminars/getListFutureSeminarsFull',
    }),
  },
}
</script>

<style scoped lang="scss">
.content-future {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
  // justify-content: center;
  ul {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-column-gap: 40px;
    grid-row-gap: 55px;
    margin-bottom: 150px;
    width: 100%;
    min-height: 500px;
    @media #{$lg} {
      grid-template-columns: repeat(2, 1fr);
    }
    @media #{$md} {
      grid-template-columns: repeat(1, 1fr);
    }
    @media #{$sm} {
      grid-template-columns: repeat(1, 1fr);
    }
    @media #{$xs} {
      grid-template-columns: repeat(1, 1fr);
    }
  }
}
.comming {
  max-width: 450px;
  line-height: 70px;
  margin-top: 32px;
  margin-bottom: 80px;
  pointer-events: none;
  margin-right: auto;
  font: {
    size: 64px;
    weight: bold;
  }
  @media #{$xs-md} {
    font-size: 34px;
    line-height: 44px;
  }
  span {
    color: $RF-main;
  }
}
.filter {
  margin-bottom: 50px;
  @media #{$xs-sm} {
    display: none;
  }
}
.filter-mobile {
  display: none;
  width: 100%;
  margin-bottom: 30px;
  @media #{$xs-sm} {
    display: flex;
    justify-content: center;
  }
  &__inner {
    border: 1px solid $RF-main;
    border-radius: 4px;
    width: 280px;
    height: 42px;
    display: flex;
    justify-content: center;
    align-items: center;
    vertical-align: middle;
    cursor: pointer;
    .lines {
      width: 26px;
      &_1 {
        width: 26px;
        border-bottom: 2px solid $RF-main;
      }
      &_2 {
        width: 18px;
        margin: 4px auto;
        border-bottom: 2px solid $RF-main;
      }
      &_3 {
        width: 8px;
        margin: 4px auto;
        border-bottom: 2px solid $RF-main;
      }
    }
    .text-filters {
      color: $RF-main;
      margin-left: 8px;
      margin-bottom: 6px;
    }
  }
}
</style>
