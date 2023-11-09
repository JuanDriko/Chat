<template>
  <div id="chat" class="container">
        <h1>Chat</h1>
        <div v-for="(message, key) in messages" :key="key" class="card">
            <div class="card-body">
                <h6 class="card-subtitle mb-2 text-muted">{{ message.nickname }}</h6>
            
                <p v-if="!editaMessage || editaMessage !== idChat[key]" class="card-text">{{ message.text }}</p>             
            
                <div v-if="!editaMessage || editaMessage !== idChat[key]">
                    <button @click.prevent="editMessage(idChat[key])" href="#" class="btn btn-primary mr-2">Editar</button>
                    <button @click.prevent="deleteMessage(idChat[key])" href="#" class="btn btn-danger">Eliminar</button>
                </div>
                <div v-else>
                    <textarea v-model="message.text" class="form-control"></textarea>
                    <button @click.prevent="updateMessage(idChat[key], message.text)" href="#" class="btn btn-primary mr-2">Confirmar</button>
                    <button @click.prevent="cancelMessage" href="#" class="btn btn-danger">Cancelar</button>
                </div>
            
            </div>
        </div>
        <hr>
        <form @submit.prevent="storeMessage">
            <div class="form-group">
                <label for="">Nombre Usuario</label>
                <input v-model="nickname" class="form-control">
            </div>
            <div class="form-group">
                <label for="">Mensaje</label>
                <textarea v-model="messagesText" class="form-control"></textarea>
            </div>
            <button type="submit" class="btn btn-primary">Enviar</button>
        </form>
    </div>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
import { getDatabase, ref, push, remove, update, onValue } from "firebase/database"
const firebaseConfig = {
    apiKey: "AIzaSyAZDnHwViT5UVdPqrXAKl-p6ZqD3jljKvk",
    authDomain: "chat-c6368.firebaseapp.com",
    databaseURL: "https://chat-c6368-default-rtdb.firebaseio.com",
    projectId: "chat-c6368",
    storageBucket: "chat-c6368.appspot.com",
    messagingSenderId: "616232112624",
    appId: "1:616232112624:web:90226e61a6d898a5d60d35",
    measurementId: "G-BS5ZQH29FS"
  };
  const app = initializeApp(firebaseConfig);
  const analytics = getAnalytics(app);
  const database = getDatabase(app);   
  const messagesRef = ref(database, 'mensages'); 
    onValue(messagesRef, (snapshot) => {
        const data = snapshot.val();
        console.log(data);
    });
  export default {
    el:"#chat",
    data() {
      return {
                messages:[],
                messagesText:'',
                nickname: 'Juandriko',
                idChat:[],
                editaMessage: null
            }
    },
    methods: {
                storeMessage(){
                    this.messages.push({text: this.messagesText, nickname: this.nickname});
                push(messagesRef, {text: this.messagesText, nickname: this.nickname});
                this.messagesText = '';
                },
                deleteMessage(key) {
                    console.log('id:',key)
                    const messageRef = ref(database, `mensages/${key}`);
                    remove(messageRef);
                },
                editMessage(message){
                    this.editaMessage = message
                    this.messagesText = message.text
                },
                cancelMessage(){
                    this.editaMessage = null
                    this.messagesText = ''
                },
                updateMessage(key, message) {
                    const messageRef = ref(database, `mensages/${key}`);
                    update(messageRef, { text: message });
                    this.editaMessage = null;
                    this.messagesText = '';
                }
            },
            created() {                 
                onValue(messagesRef, (snapshot) => {
                    const data = snapshot.val();
                    console.log(data);
                    this.idChat = (Object.keys(data)); 
                    console.log('idchat',this.idChat);
                    this.messages = Object.values(data);
                });
            },
  }
</script>

<style lang="scss" scoped>

</style>