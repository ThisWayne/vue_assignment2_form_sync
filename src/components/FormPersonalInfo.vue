<template>
  <form class="my-1" v-on:submit.prevent>
    <div class="form-group">
      <label for="personalInfoFirstName">First name</label>
      <input
        id="personalInfoFirstName"
        class="form-control"
        v-bind:value="first_name"
        v-on:input="$emit('update:first_name', $event.target.value)"
      >
      <label for="personalInfoLastName">Last name</label>
      <input
        id="personalInfoLastName"
        class="form-control"
        v-bind:value="last_name"
        v-on:input="$emit('update:last_name', $event.target.value)"
      >
    </div>
    <div class="d-flex flex-row">
      <span>Gender</span>
      <div class="d-flex flex-column ml-3">
        <label class="mx-1" v-for="genderOption in genderOptions" v-bind:key="genderOption.value">
          <input
            type="radio"
            v-bind:checked="gender === genderOption.value"
            v-on:input="$emit('update:gender', genderOption.value)"
          >
          {{genderOption.name}}
        </label>
      </div>
    </div>
    <div class="form-group">
      <label for="personalInfoAddress">Address</label>
      <input
        id="personalInfoAddress"
        class="form-control"
        v-bind:value="address"
        v-bind:disabled="is_homeless"
        v-on:input="$emit('update:address', $event.target.value)"
      >
      <label class="my-1">
        <input type="checkbox" v-bind:checked="is_homeless" v-on:input="handleIsHomelessCheck">
        This client has no fixed address
      </label>
    </div>
    <div class="form-group">
      <label for="personalInfoJob">
        Job
        <select
          id="personalInfoJob"
          class="form-control"
          v-bind:value="personalInfoJobValue"
          v-on:change="handleJobChange"
        >
          <option
            v-for="jobOption in jobOptions"
            v-bind:key="jobOption.value"
            v-bind:value="jobOption.value"
          >{{jobOption.name}}</option>
        </select>
      </label>
    </div>
    <div class="form-group">
      <label for="personalInfoNote">
        Note
        {{noteLength}}/2000 characters
      </label>
      <textarea
        maxlength="2000"
        id="personalInfoNote"
        class="form-control"
        v-bind:value="note"
        v-on:input="$emit('update:note', $event.target.value)"
      ></textarea>
    </div>
  </form>
</template>
<script>
const jobOptions = [
  {
    name: "Secret",
    value: ""
  },
  {
    name: "Agent",
    value: "agent"
  },
  {
    name: "Secret Agent",
    value: "secret_agent"
  },
  {
    name: "Agent of Secret Agent",
    value: "agent_of_secret_agent"
  }
];

const genderOptions = [
  { name: "Secret", value: 0 },
  { name: "Male", value: 1 },
  { name: "Female", value: 2 }
];

export default {
  props: [
    "first_name",
    "last_name",
    "gender",
    "address",
    "is_homeless",
    "job",
    "note"
  ],
  data() {
    return {
      jobOptions: jobOptions,
      genderOptions: genderOptions
    };
  },
  methods: {
    handleIsHomelessCheck(e) {
      this.$emit("update:is_homeless", e.target.checked);
      this.$emit("onNoFixedAddressClick", e);
    },
    handleJobChange(e) {
      this.$emit("update:job", e.target.value);
      this.$emit("onJobSelectChange", e);
    }
  },
  computed: {
    noteLength() {
      const { note } = this.$props;
      return note ? note.length : 0;
    },
    personalInfoJobValue() {
      return this.$props.job ? this.$props.job : "";
    }
  }
};
</script>