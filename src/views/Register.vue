<template>
  <div>
    <b-button v-b-modal.modal-1>Register</b-button>

    <b-modal v-model="visible" busy id="modal-1" size="lg" title="New Account">
      <b-container>
        <b-form>
          <b-row class="form-group">
            <b-col>
              Account Name
              {{ this.user }}
              {{ this.accountType }}
              {{ this.account }}
            </b-col>
            <b-col>
              <b-input
                v-validate="'required|min:3|max:30|alpha'"
                v-model="account.name"
                type="text"
                name="account-name"
                placeholder="account name"
              />
              <li v-if="errors.first('account-name')" style="color: red">
                {{ errors.first("account-name") }}
              </li>
            </b-col>
          </b-row>
          <b-row class="form-group">
            <b-col cols="6"> Create as </b-col>
            <b-col cols="6" class="d-flex justify-content-between">
              <b-form-radio
                v-validate="'required'"
                name="account-type"
                v-model="accountType"
                :value="0"
                >New User</b-form-radio
              >
              <b-form-radio
                v-validate="'required'"
                name="account-type"
                v-model="accountType"
                :value="1"
                >Existent User</b-form-radio
              >
            </b-col>
            <b-col></b-col>
            <b-col>
              <li v-if="errors.first('account-type')" style="color: red">
                {{ errors.first("account-type") }}
              </li>
            </b-col>
          </b-row>
          <div v-if="account.type == 0">
            <b-row class="form-group">
              <b-col> Username </b-col>
              <b-col>
                <b-input
                  v-validate="'required|min:3|max:30'"
                  v-model="user.username"
                  type="text"
                  name="username"
                  placeholder="username"
                />
                <li v-if="errors.first('username')" style="color: red">
                  {{ errors.first("username") }}
                </li>
              </b-col>
            </b-row>
            <b-row class="form-group">
              <b-col> Password </b-col>
              <b-col>
                <b-input
                  v-validate="'required|min:3|max:30'"
                  v-model="user.password"
                  type="password"
                  name="password"
                  placeholder="password"
                  ref="password"
                />
                <li v-if="errors.first('password')" style="color: red">
                  {{ errors.first("password") }}
                </li>
              </b-col>
            </b-row>
            <b-row class="form-group">
              <b-col> Confirm Password </b-col>
              <b-col>
                <b-input
                  v-validate="'confirmed:password'"
                  type="password"
                  name="confirm-password"
                  placeholder="re-type password"
                />
                <li v-if="errors.first('confirm-password')" style="color: red">
                  {{ errors.first("confirm-password") }}
                </li>
              </b-col>
            </b-row>
          </div>
          <div v-else-if="account.type == 1">
            <b-row class="form-group">
              <b-col> Creator </b-col>
              <b-col>
                <b-form-select
                  v-validate="
                    `required|included:${users.map((el) => el.id).toString()}`
                  "
                  v-model="account.creator"
                  class="form-select"
                  name="creator"
                >
                  <b-form-select-option :value="undefined" selected
                    >select a user..</b-form-select-option
                  >
                  <b-form-select-option
                    v-for="(item, index) in users"
                    :value="item.id"
                    :key="index"
                    >{{ item.name }}</b-form-select-option
                  >
                </b-form-select>
                <li v-if="errors.first('creator')" style="color: red">
                  {{ errors.first("creator") }}
                </li>
              </b-col>
            </b-row>
          </div>
          <b-row class="form-group">
            <b-col> Measurement </b-col>
            <b-col>
              <b-form-select
                v-validate="
                  `required|included:${measurements
                    .map((el) => el.id)
                    .toString()}`
                "
                v-model="account.measurement"
                class="form-select"
                name="measurement"
              >
                <b-form-select-option :value="undefined" selected
                  >select a measurement..</b-form-select-option
                >
                <b-form-select-option
                  v-for="(item, index) in measurements"
                  :value="item.id"
                  :key="index"
                  >{{ item.type }}</b-form-select-option
                >
              </b-form-select>
              <li v-if="errors.first('measurement')" style="color: red">
                {{ errors.first("measurement") }}
              </li>
            </b-col>
          </b-row>
          <b-row class="form-group">
            <b-col> Billing plan </b-col>
            <b-col>
              <b-form-select
                v-validate="
                  `required|included:${billPlans.map((el) => el.id).toString()}`
                "
                v-model="account.billPlan"
                class="form-select"
                name="bill-plan"
              >
                <b-form-select-option :value="undefined"
                  >select a billing plan..</b-form-select-option
                >
                <b-form-select-option
                  v-for="(item, index) in billPlans"
                  :value="item.id"
                  :key="index"
                  >{{ item.period }}</b-form-select-option
                >
              </b-form-select>
              <li v-if="errors.first('bill-plan')" style="color: #ff0000">
                {{ errors.first("bill-plan") }}
              </li>
            </b-col>
          </b-row>
          <b-row class="form-group">
            <b-col>
              <b-textarea
                v-validate="'max:400'"
                v-model="account.note"
                placeholder="Note.."
                name="note"
              />
              <li v-if="errors.first('note')" style="color: red">
                {{ errors.first("note") }}
              </li>
            </b-col>
          </b-row>
        </b-form>
      </b-container>
      <template #modal-footer>
        <b-row class="w-100">
          <b-col class="d-flex justify-content-between">
            <b-button @click="validateForm" variant="success">Create</b-button>
            <b-button @click="cancel" variant="danger">Cancel</b-button>
          </b-col>
        </b-row>
      </template>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: "Register.vue",

  data() {
    return {
      visible: false,
      account: {},
      accountType: "",
      user: {},
      users: [
        { id: 1, name: "user1" },
        { id: 2, name: "user2" },
        { id: 3, name: "user3" },
      ],
      measurements: [
        { id: 1, type: "metric" },
        { id: 2, type: "imperial" },
        { id: 3, type: "aass" },
      ],
      billPlans: [
        { id: 1, period: "monthly" },
        { id: 2, period: "3 months" },
        { id: 3, period: "6 months" },
      ],
    };
  },
  methods: {
    validateForm() {
      this.$validator.validate().then((valid) => {
        if (valid) {
          if (this.account.type === 0) {
            //send request to create new user and get the user ID on response
          }
          //send request to create a new account
          this.resetAll();
          this.$bvToast.toast("User have been created successfully!", {
            title: "Success",
            variant: "success",
            autoHideDelay: 5000,
            solid: true,
          });
        }
      });
    },
    cancel() {
      this.resetAll();
    },
    resetAll() {
      this.account = {};
      this.user = {};
      this.visible = false;
    },
  },
  watch: {
    accountType: function (value) {
      this.account.type = value;
      if (value === 0) {
        if (this.account.creator) delete this.account.creator;
      }
      if (value === 1) {
        this.user = {};
      }
    },
  },
};
</script>

<style scoped>
.form-group {
  margin-bottom: 10px;
  margin-top: 10px;
}
</style>
