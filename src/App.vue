<template>
  <div id="app">
    <div v-for="message in messages">
      <input v-model="message.text" type="text" />
      <button @click="updateMessage(message)">update</button>
      <button @click="deleteMessage(message.id)">delete</button>
    </div>
    <hr />
    <input ref="newmessage" placeholder="new message ..." type="text" />
    <button @click="addNewMessage">addnew</button>
  </div>
</template>

<script>
import {
  getFirestore,
  onSnapshot,
  collection,
  doc,
  deleteDoc,
  setDoc,
  addDoc,
  orderBy,
  query,
} from 'firebase/firestore';
import { ref, onUnmounted } from 'vue';
import HelloWorld from './components/HelloWorld.vue';
// Import the functions you need from the SDKs you need
import { initializeApp } from 'firebase/app';
import { getAnalytics } from 'firebase/analytics';
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: 'AIzaSyD7wUAsLl_2Bzwr6i68XNlMthWSY7vGyRc',
  authDomain: 'database-8771.firebaseapp.com',
  projectId: 'database-8771',
  storageBucket: 'database-8771.appspot.com',
  messagingSenderId: '1002999353104',
  appId: '1:1002999353104:web:c641370a3c928d710edf95',
  measurementId: 'G-N2NZZ5C0W3',
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);
const db = getFirestore(app);
export default {
  name: 'App',
  components: {},
  methods: {
    addNewMessage: function () {
      addDoc(collection(db, 'messages'), {
        text: this.$refs.newmessage.value,
        date: Date.now(),
      });
    },
    updateMessage: function (message) {
      setDoc(doc(db, 'messages', message.id), {
        text: message.text,
        date: message.date,
      });
    },
    deleteMessage: function (id) {
      deleteDoc(doc(db, 'messages', id));
    },
  },
  data: () => {
    return {
      messages: ref([]),
    };
  },
  mounted() {
    const latestQuery = query(collection(db, 'messages'), orderBy('date'));
    const livemessages = onSnapshot(latestQuery, (snapshot) => {
      this.messages = snapshot.docs.map((doc) => {
        return {
          id: doc.id,
          text: doc.data().text,
          date: doc.data().date,
        };
      });
    });
    onUnmounted(livemessages);
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
