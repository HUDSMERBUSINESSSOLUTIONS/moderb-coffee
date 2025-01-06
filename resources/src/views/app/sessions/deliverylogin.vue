<template>
    <div class="delivery-login">
      <h1>Delivery Login Page</h1>
      <p>Please enter your delivery credentials below:</p>
      <validation-observer ref="delivery_login">
        <b-form @submit.prevent="submitDeliveryLogin">
          <validation-provider
            name="Email Address"
            :rules="{ required: true }"
            v-slot="validationContext"
          >
            <b-form-group :label="'Email Address'" class="text-12">
              <b-form-input
                :state="getValidationState(validationContext)"
                aria-describedby="Email-feedback"
                class="form-control-rounded"
                type="text"
                v-model="deliveryEmail"
                email
              ></b-form-input>
              <b-form-invalid-feedback id="Email-feedback">{{ validationContext.errors[0] }}</b-form-invalid-feedback>
            </b-form-group>
          </validation-provider>
  
          <validation-provider
            name="Password"
            :rules="{ required: true }"
            v-slot="validationContext"
          >
            <b-form-group :label="'Password'" class="text-12">
              <b-form-input
                :state="getValidationState(validationContext)"
                aria-describedby="Password-feedback"
                class="form-control-rounded"
                type="password"
                v-model="deliveryPassword"
              ></b-form-input>
              <b-form-invalid-feedback id="Password-feedback">{{ validationContext.errors[0] }}</b-form-invalid-feedback>
            </b-form-group>
          </validation-provider>
  
          <b-button
            type="submit"
            tag="button"
            class="btn-rounded btn-block mt-2"
            variant="primary mt-2"
            :disabled="loading"
          >Login</b-button>
          <div v-if="loading">
            <div class="spinner sm spinner-primary mt-3"></div>
          </div>
        </b-form>
      </validation-observer>
    </div>
  </template>
  
  <script>
  import { ValidationObserver, ValidationProvider } from 'vee-validate';
  
  export default {
    name: 'DeliveryLogin',
    components: {
      ValidationObserver,
      ValidationProvider
    },
    data() {
      return {
        deliveryEmail: '',
        deliveryPassword: '',
        loading: false
      };
    },
    methods: {
      getValidationState({ dirty, validated, valid = null }) {
        return dirty || validated ? valid : null;
      },
      submitDeliveryLogin() {
        this.$refs.delivery_login.validate().then(success => {
          if (!success) {
            this.makeToast("danger", "Please fill the form correctly", "Failed");
          } else {
            this.loading = true;
            // Here you would typically send the login request to your backend
            // For example:
            axios.post('/delivery-login', {
              email: this.deliveryEmail,
              password: this.deliveryPassword
            })
            .then(response => {
              this.makeToast("success", "Successfully logged in", "Success");
              // Redirect or perform other actions after successful login
            })
            .catch(error => {
              this.loading = false;
              this.makeToast("danger", "Login failed", "Failed");
            });
          }
        });
      },
      makeToast(variant, msg, title) {
        this.$root.$bvToast.toast(msg, {
          title: title,
          variant: variant,
          solid: true
        });
      }
    }
  };
  </script>
  
  <style scoped>
  .delivery-login {
    text-align: center;
    margin-top: 50px;
  }
  </style>