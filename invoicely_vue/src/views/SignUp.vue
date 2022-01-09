<template>
  <div class="page-signup">
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <h1 class="title">Sign up</h1>

        <form @submit.prevent="submitForm">
          <div class="field">
            <label for="email">Email</label>
            <div class="control">
              <input
                type="email"
                class="input"
                v-model="username"
                placeholder="Email"
                required
              />
            </div>
          </div>

          <div class="field">
            <label for="password1">Password</label>
            <div class="control">
              <input
                type="password"
                class="input"
                v-model="password1"
                placeholder="Password"
                required
              />
            </div>
          </div>

          <div class="field">
            <label for="password2">Repeat Password</label>
            <div class="control">
              <input
                type="password"
                class="input"
                v-model="password2"
                placeholder="Re-password"
                required
              />
            </div>
          </div>

          <div class="notification is-danger" v-if="errors.length">
            <p v-for="error in errors" v-bind:key="error">
              {{ error }}
            </p>
          </div>

          <div class="field">
            <div class="control">
              <button class="button is-success is-fullwidth is-outlined">
                Submit
              </button>
            </div>
          </div>
        </form>

        <hr />

        <router-link :to="{ name: 'LogIn' }">Click here</router-link> to log in
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios"

export default {
  name: "SignUp",
  data() {
    return {
      username: "",
      password1: "",
      password2: "",
      errors: [],
    }
  },
  methods: {
    submitForm() {
      if (this.password2 !== this.password1) {
        this.errors.push("Passwords don't match!")
      } else {
        const formData = {
          username: this.username,
          password: this.password1,
        }

        axios
          .post("/api/v1/users/", formData)
          .then((response) => {
            this.$router.push({ name: "LogIn" })
          })
          .catch((error) => {
            if (error.response.data) {
              for (const property in error.response.data) {
                this.errors.push(`${error.response.data[property]}`)
              }
            } else {
              this.errors.push("Unable to sign up with bad credentials.")
            }
          })
      }
    },
  },
}
</script>
