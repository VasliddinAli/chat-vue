<template>
  <div class="register">
    <h1>Sign Up</h1>
    <input v-model="name" id="name" type="text" placeholder="Enter name" />
    <input v-model="phone" id="phone" type="text" placeholder="Enter phone" />
    <button @click="signUp">Sign Up</button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      name: "",
      phone: "",
    };
  },
  methods: {
    async signUp() {
      if (this.name && this.phone) {
        let result = await axios.post("https://chat-api.up.railway.app/user", {
          name: this.name,
          phone: this.phone,
        });
        if (result.status == 201) {
          localStorage.setItem("user-info", JSON.stringify(result.data));
          this.$router.push({ name: "Home" });
        }
      }
    },
  },
  mounted() {
    {
      let user = localStorage.getItem("user-info");
      if (user) {
        this.$router.push({ name: "Home" });
      }
    }
  },
};
</script>
