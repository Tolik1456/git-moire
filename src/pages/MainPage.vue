<template>
  <main class="content container">
    <div class="content__row catalog-margin">
      <h1 class="content__title">
        Каталог
      </h1>
      <span class="content__info">
        {{ countProducts }} товара
      </span>
    </div>

    <div class="content__catalog">
      <ProductFilter :price-from.sync="filterPriceFrom" :price-to.sync="filterPriceTo"
        :category-id.sync="filterCategoryId" :color-ids.sync="filterColorIds" :material-ids.sync="filterMaterialIds"
        :season-ids.sync="filterSeasonIds" :show-reset.sync="showReset" />
      <section class="catalog">
        <div class="loader" v-if="productsLoading"></div>
        <div v-if="productsLoadingFalled">Произошла ошибка при загрузке товаров <button
            @click.prevent="loadProducts()">Попробовать еще раз</button></div>
        <ProductList :products="products" />
        <BasePagination :page.sync="page" :count="countProducts" :per-page="productsPerPage" />
      </section>
    </div>
  </main>
</template>
<script>

import ProductList from '@/components/ProductList.vue';
import BasePagination from '@/components/BasePagination.vue';
import ProductFilter from '@/components/ProductFilter.vue';
import axios from 'axios';
import { API_BASE_URL } from '@/config';

export default {
  components: { ProductList, BasePagination, ProductFilter },
  data() {
    return {
      filterPriceFrom: 0,
      filterPriceTo: 0,
      filterCategoryId: 0,
      filterMaterialIds: [],
      filterSeasonIds: [],
      filterColorIds: [],
      page: 1,
      productsPerPage: 12,
      productsData: null,
      productsLoading: false,
      productsLoadingFalled: false,
      showReset: true,
    };
  },
  computed: {
    products() {
      return this.productsData
        ? this.productsData.items.map(product => {
          return {
            ...product
          }
        })
        : [];
    },
    countProducts() {
      return this.productsData ? this.productsData.pagination.total : 0;
    },
  },
  methods: {
    loadProducts() {
      this.productsLoading = true;
      this.productsLoadingFailed = true;
      clearTimeout(this.loadProductsTimer);
      this.loadProductsTimer = setTimeout(() => {
        axios.get(API_BASE_URL + '/api/products', {
          params: {
            page: this.page,
            limit: this.productsPerPage,
            categoryId: this.filterCategoryId,
            materialIds: this.filterMaterialIds,
            seasonIds: this.filterSeasonIds,
            colorIds: this.filterColorIds,
            minPrice: this.filterPriceFrom,
            maxPrice: this.filterPriceTo,
          },
        })
          .then(response => this.productsData = response.data)
          .catch(() => this.productsLoadingFailed = true)
          .then(() => this.productsLoading = false);
      }, 0);
    },
  },
  watch: {
    page() {
      this.loadProducts();
    },
    filterPriceFrom() {
      this.loadProducts();
    },
    filterPriceTo() {
      this.loadProducts();
    },
    filterCategoryId() {
      this.loadProducts();
    },
    filterMaterialIds() {
      this.loadProducts();
    },
    filterSeasonIds() {
      this.loadProducts();
    },
    filterColorIds() {
      this.loadProducts();
    },
  },
  created() {
    if (this.$route.query.filterCategoryId) {
      this.filterCategoryId = this.$route.query.filterCategoryId;
      this.showReset=false;
    }
    this.loadProducts();
  },
};
</script>
