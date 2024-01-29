<template>
  <div class="container">
    <h1>後台商品頁面</h1>
    <h2>WEEK 4</h2>

    <div class="text-end mt-4">
      <button class="btn btn-primary" @click="openModal('create')">建立新的產品</button>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th scope="col">商品分類</th>
          <th scope="col">商品名稱</th>
          <th scope="col">原價</th>
          <th scope="col">售價</th>
          <th scope="col">星級評分</th>
          <th scope="col">是否啟用</th>
          <th scope="col">編輯</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in products" :key="item.id">
          <td>{{ item.title }}</td>
          <td>
            {{ item.category }}
          </td>
          <td>
            {{ item.origin_price }}
          </td>
          <td>
            {{ item.price }}
          </td>
          <td>
            {{ item.rating }}
          </td>
          <td>
            <span class="text-success" v-if="item.is_enabled">啟用</span>
            <span v-else>未啟用</span>
          </td>
          <td>
            <button type="button" @click="viewProduct(item)">查看詳情</button
            ><button type="button" @click="openModal('edit', item)">編輯</button>
            <button type="button" @click="deleteProduct(item.id)">刪除</button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- <nav aria-label="Page navigation example">
      <ul class="pagination">
        <li class="page-item" :class="{ disabled: !pagination.has_pre }">
          <a class="page-link" href="#" @click.prevent="getProducts(pagination.current_page - 1)"
            >&lt;</a
          >
        </li>
        <li class="page-item" v-for="page in pagination.total_pages" :key="page + 2123">
          <a
            class="page-link"
            href="#"
            @click.prevent="getProducts(page)"
            :class="{ active: page === pagination.current_page }"
            >{{ page }}</a
          >
        </li>
        <li class="page-item" :class="{ disabled: !pagination.has_next }">
          <a class="page-link" href="#" @click.prevent="getProducts(pagination.current_page + 1)"
            >></a
          >
        </li>
      </ul>
    </nav> -->
    <Pagination :pagination="pagination" :get-products="getProducts"></Pagination>
    <h2>查看商品詳情(樣式未來會在更改)</h2>
    {{ product }}
  </div>
  <BsModelComponent ref="bsModalComponent" :set-data="tempData" :get-products="getProducts" />
</template>

<script>
import axios from 'axios';
import BsModelComponent from '@/components/BsModelComponent.vue';
import Pagination from '@/components/Pagination.vue';

export default {
  data() {
    return {
      products: [],
      product: {},
      tempData: '',
      pagination: []
    };
  },
  components: {
    BsModelComponent,
    Pagination
  },
  methods: {
    openModal(type, data) {
      if (type === 'create') {
        this.tempData = {
          id: '',
          title: '',
          category: '',
          unit: '',
          imageUrl: '',
          price: '',
          origin_price: '',
          description: '',
          content: '',
          is_enabled: 1,
          rating: 0
        };
        this.$refs.bsModalComponent.openModal();
      } else {
        // 編輯就直接將點選資料帶入。
        this.tempData = data;
        this.$refs.bsModalComponent.openModal();
      }
    },
    async getProducts(page = 1) {
      try {
        const api = `${import.meta.env.VITE_BASE_API_URL}/v2/api/${
          import.meta.env.VITE_API_PATH
        }/admin/products?page=${page}`;
        const response = await axios.get(api);
        const { products, pagination } = response.data;
        this.pagination = pagination;
        this.products = Object.values(products);
      } catch (error) {
        console.log(error?.response?.data?.message);
      }
    },
    viewProduct(product) {
      this.product = product;
    },
    async deleteProduct(id) {
      try {
        if (window.confirm('您確定要執行這個操作嗎？')) {
          console.log('確認刪除');
          const deleteApi = `${import.meta.env.VITE_BASE_API_URL}/v2/api/${import.meta.env.VITE_API_PATH}/admin/product/${id}`;
          const response = await axios.delete(deleteApi);
          const { success, message } = response.data;
          if (success) {
            console.log(message);
            this.getProducts();
          }
        } else {
          console.log('取消');
        }
      } catch (error) {
        console.log(error);
      }
    }
  },
  mounted() {
    this.getProducts();
  }
};
</script>
