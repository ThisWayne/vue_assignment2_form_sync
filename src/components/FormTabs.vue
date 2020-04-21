<template>
  <nav>
    <ul class="nav nav-tabs">
      <li class="nav-item" v-for="record in records" v-bind:key="record.tab_sequence">
        <a
          class="nav-link"
          v-bind:href="record.tab_name"
          v-bind:class="{active: activeTabSeq === record.tab_sequence}"
          v-on:click.prevent="$emit('onFormTabClick', record.tab_sequence)"
        >{{tabNameMap[record.tab_name]}}</a>
      </li>
    </ul>
  </nav>
</template>
<script>
const tabNameMap = {
  personal_info: "Personal Info",
  orders: "Orders",
  overview: "Overview"
};
export default {
  name: "FormTabs",
  props: {
    records: {
      type: Array,
      required: true,
      default() {
        return [];
      }
    },
    activeTabSeq: {
      type: Number,
      required: true,
      default() {
        return -1;
      }
    }
  },
  data() {
    return {
      tabNameMap: tabNameMap
    };
  }
};
</script>