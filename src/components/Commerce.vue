<template>
  <div v-if="items" class="flex-col items-center justify-start">
    <div class="mb-5 flex w-full flex-row items-start px-5">
      <div class="hidden w-[15%] pt-5 md:block">
        <LeftSideBar />
      </div>
      <div class="flex w-full flex-col items-start pt-5 md:w-[70%]">
        <h2 class="text-[#FFFFFF75]">Showing {{ items.length }} Results</h2>
        <div class="mt-5 flex flex-row flex-wrap items-start">
          <router-link
            :key="item.path"
            v-for="item in items"
            :to="`/product/${item.path.replace(/\//g, '')}`"
            class="relative mt-2 w-full border border-white p-1 sm:w-1/2 md:w-1/3"
          >
            <Prefetch :url="`/l0-api/products/${item.path.replace(/\//g, '')}`"><span class="h-0 w-0"></span></Prefetch>
            <div class="absolute top-0 left-0 z-10 flex flex-col items-start">
              <h3 class="bg-white py-2 px-4 text-xl font-medium text-black">{{ item.name }}</h3>
              <h4 class="text-md bg-white py-2 px-4 text-black">{{ item.prices.price.value }} {{ item.prices.price.currencyCode }}</h4>
            </div>
            <HeartIcon className="absolute top-0 right-0 h-[30px] w-[30px] bg-white p-2" />
            <img class="h-[300px] object-cover" width="1200" height="1200" loading="lazy" :src="getImage(item.images[0].url)" />
          </router-link>
        </div>
      </div>
      <div class="hidden w-[15%] pt-5 md:block">
        <RightSideBar />
      </div>
    </div>
  </div>
</template>

<script>
import Prefetch from '@layer0/vue'
import HeartIcon from './HeartIcon.vue'
import LeftSideBar from './LeftSideBar.vue'
import RightSideBar from './RightSideBar.vue'
import { filterProducts, relativizeURL } from '../../lib/helper'

export default {
  name: 'Commerce',
  components: {
    Prefetch,
    HeartIcon,
    LeftSideBar,
    RightSideBar,
  },
  data: () => {
    return {
      items: [],
    }
  },
  watch: {
    '$route.query.filter': {
      handler: function (filter) {
        this.setProducts(this.$route.params.slug, filter)
      },
      deep: true,
      immediate: true,
    },
    '$route.params.slug': {
      handler: function (slug) {
        this.setProducts(slug, this.$route.query.filter)
      },
      deep: true,
      immediate: true,
    },
  },
  methods: {
    getImage: (url) => relativizeURL(url),
    setProducts(slug, query) {
      let link = window.location.origin
      if (slug) {
        fetch(`${link}/l0-api/categories/${slug}`)
          .then((res) => res.json())
          .then((res) => {
            this.items = filterProducts(res['items'], query)
          })
      } else {
        fetch(`${link}/l0-api/products/all`)
          .then((res) => res.json())
          .then((res) => {
            this.items = filterProducts(res, query)
          })
      }
    },
  },
}
</script>
