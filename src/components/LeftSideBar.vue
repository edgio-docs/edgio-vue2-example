<template>
  <div class="flex w-full flex-col">
    <div class="flex flex-col">
      <router-link to="/commerce">
        <h3
          class="text-md mt-2"
          :class="{
            'font-medium text-[#FFFFFF]': $route.path === `/commerce`,
            'font-light text-[#FFFFFF75]': $route.path !== `/commerce`,
          }"
        >
          Shop All
        </h3>
      </router-link>
      <router-link :key="item.slug" :to="`/commerce/${item.slug}`" v-for="item in listingItems">
        <h3
          class="text-md mt-2"
          :class="{
            'font-medium text-[#FFFFFF]': $route.path === `/commerce/${item.slug}`,
            'font-light text-[#FFFFFF75]': $route.path !== `/commerce/${item.slug}`,
          }"
        >
          {{ item.name }}
        </h3>
      </router-link>
    </div>
  </div>
</template>

<script>
export default {
  data: () => {
    return {
      listingItems: [],
    }
  },
  methods: {
    setListingItems(data) {
      this.listingItems = data
    },
  },
  mounted() {
    fetch('/l0-api/categories/all')
      .then((res) => res.json())
      .then((res) => {
        this.setListingItems(res)
      })
  },
}
</script>