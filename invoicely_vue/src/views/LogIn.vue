<template>
  <div class="container">
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <h1 class="title">Log in</h1>

        <form @submit.prevent="submitForm">
          <div class="field">
            <label for="email">Email</label>
            <div class="control">
              <input
                type="email"
                name="email"
                class="input"
                v-model="username"
                placeholder="Email"
                required
              />
            </div>
          </div>

          <div class="field">
            <label for="password">Password</label>
            <div class="control">
              <input
                type="password"
                name="password"
                class="input"
                v-model="password"
                placeholder="Password"
                required
              />
            </div>
          </div>

          <div class="notification is-danger" v-if="errors.length">
            <p v-for="error in errors" :key="error">{{ error }}</p>
          </div>

          <div class="field">
            <div class="control">
              <button class="button is-success is-outlined is-fullwidth">
                Submit
              </button>
            </div>
          </div>
        </form>

        <hr />

        <router-link :to="{ name: 'SignUp' }">Click here</router-link> to sign
        up
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios"

export default {
  name: "LogIn",
  data() {
    return {
      username: "",
      password: "",
      errors: [],
    }
  },
  methods: {
    async submitForm() {
      axios.defaults.headers.common["Authorization"] = ""
      localStorage.removeItem("token")

      this.errors = []

      const formData = {
        username: this.username,
        password: this.password,
      }

      await axios
        .post("/api/v1/token/login/", formData)
        .then((response) => {
          const token = response.data.auth_token

          this.$store.commit("setToken", token)
          axios.defaults.headers.common["Authorization"] = "Token " + token
          localStorage.setItem("token", token)
        })
        .catch((error) => {
          if (error.response) {
            for (const property in error.response.data) {
              this.errors.push(`${error.response.data[property]}`)
            }
          } else {
            this.errors.push("Unable to sign in with bad credentials.")
          }
        })

      axios.get("/api/v1/users/me/").then((response) => {
        this.$store.commit("setUser", {
          username: response.data.username,
          id: response.data.id,
        })

        localStorage.setItem("username", response.data.username)
        localStorage.setItem("userid", response.data.id)

        this.$router.push("/dashboard")
      })
    },
  },
}
</script>
