<template>
  <div class="my-1">
    <span>followings are {{fullname}} order:</span>
    <ul>
      <li v-show="!(orders.apple_count || orders.banana_condiments.length > 0)">No data found</li>
      <li v-show="orders.apple_count">{{orders.apple_count}} apples</li>
      <li v-show="orders.banana_condiments.length > 0">Banana condiments
        <ul>
          <li v-for="condiment in orders.banana_condiments" v-bind:key="condiment">{{condiment}}</li>
        </ul>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  props: {
    personalInfo: {
      type: Object,
      default() {
        return {};
      }
    },
    orders: {
      type: Object,
      default() {
        return {
          banana_condiments: []
        };
      }
    }
  },
  computed: {
    fullname() {
      let fullname = "unknown";
      if (!this.$props.personalInfo) {
        return fullname;
      }

      const { first_name, last_name } = this.$props.personalInfo;
      if (first_name) {
        fullname = last_name ? `${first_name} ${last_name}` : first_name;
      } else if (last_name) {
        fullname = last_name;
      }

      return fullname;
    },
    isOrderExist() {
      return this.$props.orders !== undefined;
    }
  }
};
</script>

