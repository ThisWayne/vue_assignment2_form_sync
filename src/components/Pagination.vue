<template>
  <nav aria-label="Page navigation example">
    <ul class="pagination">
      <li class="page-item" v-bind:class="{disabled: currentFormInfoIndex <= 0}">
        <a
          class="page-link"
          href="prev"
          v-bind:title="prevFormId"
          v-on:click.prevent="$emit('onPrevPageClick', $event)"
        >Previous</a>
      </li>
      <li
        class="page-item"
        v-for="(formInfo, index) in formInfos"
        v-bind:key="formInfo.id"
        v-bind:class="{active: currentFormInfoIndex === index}"
      >
        <a
          class="page-link"
          href="page"
          v-bind:title="formInfo.id"
          v-on:click.prevent="$emit('onPageClick', index)"
        >{{index + 1}}</a>
      </li>
      <li class="page-item" v-bind:class="{disabled: currentFormInfoIndex >= formInfos.length - 1}">
        <a
          class="page-link"
          href="next"
          v-bind:title="nextFormId"
          v-on:click.prevent="$emit('onNextPageClick', $event)"
        >Next</a>
      </li>
    </ul>
  </nav>
</template>
<script>
export default {
  props: {
    formInfos: {
      type: Array,
      required: true
    },
    currentFormInfoIndex: {
      type: Number,
      required: true
    }
  },
  computed: {
    prevFormId() {
      const { formInfos, currentFormInfoIndex } = this.$props;
      return currentFormInfoIndex - 1 >= 0
        ? formInfos[currentFormInfoIndex - 1].id
        : "";
    },
    nextFormId() {
      const { formInfos, currentFormInfoIndex } = this.$props;
      return currentFormInfoIndex + 1 < formInfos.length
        ? formInfos[currentFormInfoIndex + 1].id
        : "";
    }
  }
};
</script>

