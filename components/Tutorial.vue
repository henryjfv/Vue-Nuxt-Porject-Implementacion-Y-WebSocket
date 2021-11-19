<template>
  <div>
    <ul id="messages" v-for="msg in messages" :key="msg">
      {{
        msg
      }}
    </ul>
    <div id="form">
      <input
        id="input"
        autocomplete="off"
        class="form-control"
        v-model="message"
      />
      <button class="btn btn-primary" @click="submit">Enviar</button>
    </div>
  </div>
</template>

<script>
import * as Y from "yjs";
import { WebsocketProvider } from "y-websocket";
export default {
  data() {
    return {
      doc: null,
      provider: null,
      message: "",
      messages: [],
      yMessages: null,
    };
  },
  mounted() {
    this.doc = new Y.Doc();
    this.provider = new WebsocketProvider(
      "ws://localhost:1234",
      "misala",
      this.doc
    );
    this.yMessages = this.doc.getArray("mensajes");
    this.yMessages.observe((e) => {
      // if(e.transaction.origin) {
      this.messages = this.yMessages.toJSON();
      console.log(this.messages);
      // }
    });
  },
  methods: {
    submit() {
      this.yMessages.insert(this.messages.length, [
        { usuario: this.messages.length, mensaje: this.message },
      ]);
    },
  },
};
</script>
<style>
body {
  margin: 10;
  padding-bottom: 3rem;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica,
    Arial, sans-serif;
}

#form {
  background: rgba(0, 0, 0, 0.15);
  padding: 0.25rem;
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  display: flex;
  height: 3.5rem;
  box-sizing: border-box;
  backdrop-filter: blur(10px);
}
#input {
  border: none;
  padding: 0 1rem;
  flex-grow: 1;
  border-radius: 2rem;
  margin: 0.25rem;
}
#input:focus {
  outline: none;
}

#messages {
  list-style-type: none;
  margin: 0;
  padding: 0;
}
#messages > li {
  padding: 0.5rem 1rem;
}
#messages > li:nth-child(odd) {
  background: #efefef;
}
</style>
