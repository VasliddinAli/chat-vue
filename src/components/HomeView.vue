<template>
  <body>
    <section class="chatbox">
      <div class="chatbox-top">
        <h1>Chat</h1>
        <button @click="logout">Chiqish</button>
      </div>
      <section class="chat-window">
        <article
          v-for="item in message"
          :key="item.id"
          :class="item.class"
          id="msg-1"
        >
          <div class="msg-box">
            <div class="flr">
              <div class="messages">
                <p class="msg" id="msg-0">
                  {{ item.msg }}
                </p>
                <div class="dropdown">
                  <button
                    type="button"
                    id="dropdownMenuButton1"
                    data-bs-toggle="dropdown"
                    aria-expanded="false"
                  >
                    <i class="fa-solid fa-ellipsis-vertical"></i>
                  </button>
                  <ul
                    class="dropdown-menu"
                    aria-labelledby="dropdownMenuButton1"
                  >
                    <li @click="deleteMessage(item.id)" class="dropdown-item">
                      <i
                        class="fa-solid fa-trash"
                      ></i>
                      <!-- <i
                        @click="updateMessage(item.id)"
                        class="fa-solid fa-pen-to-square"
                      ></i> -->
                    </li>
                  </ul>
                </div>
                <p class="date">
                  {{ item.date }}
                </p>
              </div>
            </div>
          </div>
        </article>
      </section>
      <form class="chat-input add">
        <input type="text" placeholder="Enter message" v-model="msg" />
        <button type="submit" @click="addMessage">
          <img
            src="https://i.pinimg.com/originals/36/f3/d9/36f3d96d2a5adae3d8b33cd54d262b7f.png"
            alt=""
          />
        </button>
      </form>
    </section>
  </body>

  <styleMenu />
</template>


<script>
import axios from "axios";
import styleMenu from "../components/CSS/styleMenu.css";
export default {
  data() {
    return {
      timer: null,
      msg: "",
      class: "msg-container",
      message: [],
    };
  },
  methods: {
    logout() {
      localStorage.clear();
      this.$router.push({ name: "SignUp" });
    },
    async addMessage(e) {
      e.preventDefault();

      if (this.msg) {
        let userName = JSON.parse(localStorage.getItem("user-info"));
        const result = await axios.post(
          "https://chat-api.up.railway.app/message",
          {
            date: this.date,
            msg: this.msg,
            class: this.class,
            msg_id: userName.phone,
          },
          (this.msg = "")
        );
        if (result.status == 201) {
          this.loadData();
        }
      }
    },
    async deleteMessage(id) {
      let result = await axios.delete(
        "https://chat-api.up.railway.app/message/" + id
      );
      if (result.status == 200) {
        this.loadData();
      }
    },

    async loadData() {
      let user = localStorage.getItem("user-info");
      if (!user) {
        this.$router.push({ name: "SignUp" });
      }
      let result = await axios.get("https://chat-api.up.railway.app/message");
      this.message = result.data;

      
      let container = document.querySelector(".chat-window");
        let scrollHeight = container.scrollHeight;
        container.scrollTop = scrollHeight;
    },
  },
  async mounted() {
    let d = new Date();
    this.date = `${d.getHours()}:${d.getMinutes()}`;

    // let userName = JSON.parse(localStorage.getItem("user-info"))
    // this.userName = userName.name

    this.loadData();
    this.timer = setInterval(() => {
      this.loadData();
    }, 500);
  },

  components: {
    styleMenu,
  },
};
</script>