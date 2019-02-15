<template>
  <div>
    <div class="col-4" v-for="(msg, index) in even(messages)" :key="index">
      <div class="card">
        <div class="card-body">จำนวน {{ msg.message }}</div>
      </div>
    </div>
  </div>
</template>
<style>
.slider {
  overflow-y: hidden;
  max-height: 500px; /* approximate max height */

  transition-property: all;
  transition-duration: 0.5s;
  transition-timing-function: cubic-bezier(0, 1, 0.5, 1);
}
</style>

<script>
import io from "socket.io-client";

export default {
  data() {
    return {
      user: "",
      message: "",
      messages: [],
      counter: 0,
      socket: io("localhost:3001")
    };
  },
  methods: {
    even: function(arr) {
      // Set slice() to avoid to generate an infinite loop!
      return arr.slice().sort(function(a, b) {
        return   b.message - a.message;
      });
    },
    sendMessage(e) {
      e.preventDefault();
      this.socket.emit("SEND_MESSAGE", {
        user: this.user,
        message: this.message
      });
      this.message = "";
    },
    resendMe() {
      let num = 0;
      this.socket.emit("SEND_MESSAGE", {
        user: "test",
        message: this.counter++
      });
    }
  },
  mounted() {
    this.socket.on("MESSAGE", data => {
      this.messages = [...this.messages, data];

      if (this.messages.length >= 3) this.messages.shift();
      // you can also do this.messages.push(data)
    });
    setInterval(this.resendMe, 1000);
  },
  created() {}
};
</script>

<style>
</style>
