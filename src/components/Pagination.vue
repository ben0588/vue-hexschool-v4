<template>
  <nav aria-label="Page navigation example">
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
  </nav>
</template>
<script>
export default {
  props: ['pagination', 'getProducts']
};
</script>
<style lang="sass"></style>
