<template>
  <div class="container">
    <h1>Form {{selectedFormInfoId}}</h1>
    <div class="my-1">
      <label for="selectFormId">Jump to</label>
      <select
        id="selectFormId"
        class="mx-1"
        v-bind:value="selectedFormInfoId"
        v-on:change="handleFormIdSelectChange"
      >
        <option
          v-for="formInfo in formInfos"
          v-bind:key="formInfo.id"
          v-bind:value="formInfo.id"
        >{{formInfo.id}}</option>
      </select>
    </div>
    <div>
      <button
        type="button"
        class="btn btn-primary m-1"
        v-on:click="handleLogThisFormClick"
      >Log this form</button>
      <button
        type="button"
        class="btn btn-info m-1"
        v-on:click="handleLogAllFormsClick"
      >Log all forms</button>
      <button
        type="button"
        class="btn btn-danger m-1"
        v-show="formInfos.length > 1"
        v-on:click="handleDeleteThisFormClick"
      >Delete this form</button>
    </div>
    <main class="main my-1">
      <FormTabs
        v-bind:records="currentFormInfo ? currentFormInfo.records : undefined"
        v-bind:activeTabSeq="activeTabSeq"
        v-on:onFormTabClick="handleFormTabClick"
      ></FormTabs>
      <div class="tab-content container">
        <div
          v-for="record in currentFormInfo ? currentFormInfo.records : undefined"
          v-bind:key="record.tab_sequence"
          v-show="activeTabSeq === record.tab_sequence"
        >
          <keep-alive>
            <component
              v-bind:is="tabNameCompBindingsMap[record.tab_name].compName"
              v-bind.sync="getFormComponentBindingsByName(record.tab_name)"
              v-on="tabNameCompBindingsMap[record.tab_name].events"
            ></component>
          </keep-alive>
        </div>
      </div>
    </main>
    <footer>
      <Pagination
        class="my-1"
        v-bind:formInfos="formInfos"
        v-bind:currentFormInfoIndex="currentFormInfoIndex"
        v-on:onPrevPageClick="handlePrevPageClick"
        v-on:onPageClick="handlePageClick"
        v-on:onNextPageClick="handleNextPageClick"
      ></Pagination>
    </footer>
  </div>
</template>

<script>
import data from "./apiData/2_testData.json";
import FormTabs from "./components/FormTabs.vue";
import FormPersonalInfo from "./components/FormPersonalInfo.vue";
import FormOrders from "./components/FormOrders.vue";
import FormOverview from "./components/FormOverview.vue";
import Pagination from "./components/Pagination.vue";

const apiData = data;

export default {
  name: "App",
  components: {
    FormTabs,
    FormPersonalInfo,
    FormOrders,
    FormOverview,
    Pagination
  },
  data() {
    return {
      activeTabSeq: 0,
      currentFormInfoIndex: -1,
      formInfos: [],
      appleCountTick: 0,
      tabNameCompBindingsMap: {
        personal_info: {
          compName: "FormPersonalInfo",
          events: {
            onNoFixedAddressClick: this.handleNoFixedAddressClick,
            onJobSelectChange: this.handleJobSelectChange
          }
        },
        orders: {
          compName: "FormOrders",
          events: {
            onApplePlusMinusClick: this.handleApplePlusMinusClick,
            onAppleCountInput: this.handleAppleCountInput,
            onBananaCondimentsCheck: this.handleBananaCondimentsCheck
          }
        },
        overview: {
          compName: "FormOverview"
        }
      }
    };
  },
  computed: {
    currentFormInfo() {
      const { formInfos, currentFormInfoIndex } = this.$data;
      if (formInfos.length === 0 || currentFormInfoIndex === -1) {
        return undefined;
      }
      return formInfos[currentFormInfoIndex];
    },
    currentFormInfoActiveTabData() {
      const { activeTabSeq } = this.$data;
      return this.currentFormInfo.records.find(
        i => i.tab_sequence === activeTabSeq
      ).data;
    },
    selectedFormInfoId() {
      return this.currentFormInfo ? this.currentFormInfo.id : undefined;
    }
  },
  mounted() {
    this.$data.formInfos = apiData;
    if (this.$data.formInfos && this.$data.formInfos.length > 0) {
      this.$data.currentFormInfoIndex = 0;
    }
  },
  methods: {
    handleFormIdSelectChange(e) {
      const { value: selectedFormInfoId } = e.target;
      const { formInfos } = this.$data;
      this.$data.activeTabSeq = 0;
      this.$data.currentFormInfoIndex = formInfos.findIndex(
        x => x.id === selectedFormInfoId
      );
    },
    handleLogThisFormClick() {
      const { currentFormInfoIndex, formInfos } = this.$data;
      console.log(formInfos[currentFormInfoIndex]);
    },
    handleLogAllFormsClick() {
      const { formInfos } = this.$data;
      console.log(formInfos);
    },
    handleDeleteThisFormClick() {
      const { currentFormInfoIndex, formInfos } = this.$data;
      formInfos.splice(currentFormInfoIndex, 1);
      this.$data.currentFormInfoIndex = currentFormInfoIndex % formInfos.length;
      this.$data.activeTabSeq = 0;
    },
    handleFormTabClick(tabSeq) {
      this.$data.activeTabSeq = tabSeq;
    },
    getFormComponentBindingsByName(tabName) {
      let bindings = {};
      if (tabName === "personal_info") {
        bindings = this.currentFormInfo.records.find(
          i => i.tab_name === "personal_info"
        ).data;
      } else if (tabName === "orders") {
        bindings = this.currentFormInfo.records.find(
          i => i.tab_name === "orders"
        ).data;
        bindings.appleCountTick = this.$data.appleCountTick;
      } else {
        const personalInfo = this.currentFormInfo.records.find(
          i => i.tab_name === "personal_info"
        );
        const orders = this.currentFormInfo.records.find(
          i => i.tab_name === "orders"
        );
        bindings = {
          personalInfo: personalInfo ? personalInfo.data : undefined,
          orders: orders ? orders.data : undefined
        };
      }
      return bindings;
    },
    handleNoFixedAddressClick(e) {
      this.currentFormInfoActiveTabData.address = "";
    },
    handleJobSelectChange(e) {
      this.currentFormInfoActiveTabData.job = e.target.value;
    },
    getValidAppleCount(newAppleCount) {
      let validAppleCount = newAppleCount;
      if (validAppleCount > 100) {
        validAppleCount = 100;
      } else if (validAppleCount < 1) {
        validAppleCount = 1;
      }
      return validAppleCount;
    },
    handleApplePlusMinusClick(num) {
      const { apple_count: appleCount } = this.currentFormInfoActiveTabData;
      this.currentFormInfoActiveTabData.apple_count = this.getValidAppleCount(
        Number(appleCount) + num
      );
    },
    handleAppleCountInput(e) {
      const { value: appleCount } = e.target;
      if (Number(appleCount) >= 100) e.preventDefault();
      this.currentFormInfoActiveTabData.apple_count = this.getValidAppleCount(
        Number(appleCount)
      );
      this.$data.appleCountTick++; // force the input to re-render
    },
    handleBananaCondimentsCheck(e) {
      const [{ checked, value }, { banana_condiments }] = [
        e.target,
        this.currentFormInfoActiveTabData
      ];
      if (checked) {
        banana_condiments.push(value);
      } else {
        banana_condiments.splice(banana_condiments.indexOf(value), 1);
      }
    },
    handlePrevPageClick(e) {
      const { currentFormInfoIndex } = this.$data;
      this.$data.activeTabSeq = 0;
      this.$data.currentFormInfoIndex = Math.max(currentFormInfoIndex - 1, 0);
    },
    handlePageClick(index) {
      this.$data.activeTabSeq = 0;
      this.$data.currentFormInfoIndex = index;
    },
    handleNextPageClick(e) {
      const { currentFormInfoIndex, formInfos } = this.$data;
      this.$data.activeTabSeq = 0;
      this.$data.currentFormInfoIndex = Math.min(
        currentFormInfoIndex + 1,
        formInfos.length - 1
      );
    }
  }
};
</script>
<style lang="scss" scoped>
.main {
  border: 1px solid #ccc;
}
</style>
