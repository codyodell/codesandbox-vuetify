<template>
  <v-app>
    <v-layout>
      <v-flex xs10 offset-xs1>
        <div class="title">Form styled by Material Design (Vuetify)</div>
        <v-expansion-panels accordion>
          <v-expansion-panel>
            <v-expansion-panel-header class="headline">Add Customer</v-expansion-panel-header>
            <v-expansion-panel-content>
              <v-form ref="form" v-model="valid" :lazy-validation="lazy">
                <v-text-field
                  v-model="name"
                  :counter="12"
                  :rules="[nameRule]"
                  label="Name"
                  required
                  class="mt-required"
                ></v-text-field>
                <v-text-field v-model="masked" v-mask="mask" label="Masked (##:##)"></v-text-field>
                <v-text-field v-model="email" :rules="[emailRule]" label="Email"></v-text-field>
                <v-text-field
                  v-model="startDate"
                  type="date"
                  :rules="[v => !customerSubmitted || !!v || 'State date must be entered']"
                  required
                  class="mt-required"
                  clearable
                  label="Start Date"
                ></v-text-field>
                <v-select
                  v-model="state"
                  :items="customerStates"
                  :rules="[v => !customerSubmitted || !!v || 'State must be selected']"
                  label="State"
                  required
                  class="mt-required"
                  clearable
                ></v-select>

                <v-checkbox v-model="checkbox" label="Opt In to get notifications"></v-checkbox>

                <v-btn
                  :disabled="customerSubmitted && !valid"
                  color="success"
                  class="mr-4"
                  @click="validate"
                >Save</v-btn>

                <v-btn color="error" class="mr-4" @click="reset">Reset</v-btn>

                <v-btn color="warning" @click="resetValidation">Reset Validation</v-btn>
              </v-form>
              <pre>{{customer}} form is {{valid ? 'valid': 'invalid'}}</pre>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-flex>
    </v-layout>

    <v-snackbar v-model="snackbar">Form submitted successfully
      <v-btn color="pink" text @click="snackbar = false">Close</v-btn>
    </v-snackbar>

  </v-app>
</template>

<script>
import { mask } from "vue-the-mask";

export default {
  directives: {
    mask
  },
  data: () => ({
    mask: "##:##",
    masked: "",
    show: false,
    customerSubmitted: false,
    valid: true,
    name: "",
    email: "",
    startDate: null,
    state: null,
    checkbox: false,
    lazy: true,
    customerStates: [
      { value: "1", text: "Trial" },
      { value: "2", text: "Paying" },
      { value: "3", text: "Free" },
      { value: "4", text: "Inactive" }
    ],
    snackbar: false
  }),
  methods: {
    nameRule(v) {
      if (!this.customerSubmitted) {
        return true;
      }
      if (!v) {
        return "Name is required";
      }
      if (!(v && v.length >= 3 && v.length <= 12)) {
        return "Customer name must be 3-12 characters long";
      }
      return true;
    },
    emailRule(v) {
      if (!this.customerSubmitted) {
        return true;
      }
      if (v && !/.+@.+\..+/.test(v)) {
        return "Please enter a valid email address";
      }
      return true;
    },
    validate() {
      this.customerSubmitted = true;
      if (this.$refs.form.validate()) {
        // got valid form, submit it
        this.snackbar = true;
      }
    },
    reset() {
      this.customerSubmitted = false;
      this.$refs.form.reset();
    },
    resetValidation() {
      this.customerSubmitted = false;
      this.$refs.form.resetValidation();
    }
  },
  computed: {
    customer() {
      return {
        name: this.name,
        masked: this.masked,
        email: this.email,
        startDate: this.startDate,
        state: this.state,
        optIn: this.checkbox
      };
    }
  }
};
</script>

<style>
</style>
