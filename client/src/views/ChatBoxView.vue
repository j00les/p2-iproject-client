<script>
import { DataSnapshot } from '@firebase/database';
import ChatBubble from '@/components/ChatBubble.vue';
import db from '../db';
import { mapState } from 'pinia';
import { useYalawStore } from '../stores/yalaw';

export default {
  components: {
    ChatBubble,
  },

  data() {
    return {
      inputUsername: '',
      inputMessage: '',
      messages: [],
      fullName: '',
      name: '',
    };
  },

  // computed: {
  //   ...mapState(useYalawStore, ['fullName']),
  // },

  methods: {
    //send to database(firebase)
    sendMessage() {
      const messagesRef = db.database().ref('messages');
      // const messagesRef = db.database().ref();

      if (this.inputMessage === '' || this.inputMessage === null) return;

      const message = {
        username: this.name,
        content: this.inputMessage,
      };

      // console.log(message)

      messagesRef.push(message);
      this.inputMessage = '';
    },
  },

  mounted() {
    // console.log(this.name, this.fullName);
    const messagesRef = db.database().ref('messages');
    messagesRef.on('value', snapshot => {
      const msgData = snapshot.val();
      console.log(msgData);
      const messages = [];

      Object.keys(msgData).forEach(key => {
        // console.log(msgData[key].content);

        messages.push({
          id: key,
          username: msgData[key].username,
          content: msgData[key].content,
        });
      });

      // this.username = this.name;
      this.messages = messages;
      // console.log(this.username, 'yaglag');
      this.name = localStorage.getItem('name');
      this.fullName = localStorage.getItem('fullName');
      // console.log(this.name);
    });
    // console.log(this.name);
  },
};
</script>

<template>
  <!-- ChatBox-->
  <div
    class="flex-1 container mx-auto p:2 sm:p-6 w-3/4 justify-between flex flex-col h-screen mt-20 cont-chat"
  >
    <div
      class="flex sm:items-center justify-between py-3 border-b-2 border-gray-200"
    >
      <div class="relative flex items-center space-x-4">
        <div class="relative">
          <span class="absolute text-green-500 right-0 bottom-0">
            <svg
              width="20"
              height="20"
            >
              <circle
                cx="8"
                cy="8"
                r="8"
                fill="currentColor"
              ></circle>
            </svg>
          </span>
          <img
            src="https://images.unsplash.com/photo-1549078642-b2ba4bda0cdb?ixlib=rb-1.2.1&amp;ixid=eyJhcHBfaWQiOjEyMDd9&amp;auto=format&amp;fit=facearea&amp;facepad=3&amp;w=144&amp;h=144"
            alt=""
            class="w-10 sm:w-16 h-10 sm:h-16 rounded-full"
          />
        </div>
        <div class="flex flex-col leading-tight">
          <div class="text-2xl mt-1 flex items-center">
            <span class="text-gray-700 mr-3">{{ this.fullName }}</span>
          </div>
          <span class="text-lg text-gray-600">Junior Developer</span>
        </div>
      </div>
      <div class="flex items-center space-x-2">
        <button
          type="button"
          class="inline-flex items-center justify-center rounded-lg border h-10 w-10 transition duration-500 ease-in-out text-gray-500 hover:bg-gray-300 focus:outline-none"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            class="h-6 w-6"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"
            ></path>
          </svg>
        </button>
        <button
          type="button"
          class="inline-flex items-center justify-center rounded-lg border h-10 w-10 transition duration-500 ease-in-out text-gray-500 hover:bg-gray-300 focus:outline-none"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            class="h-6 w-6"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9"
            ></path>
          </svg>
        </button>
      </div>
    </div>

    <!-- chat bubble -->
    <div
      id="messages"
      class="flex flex-col space-y-4 p-3 overflow-y-auto scrollbar-thumb-blue scrollbar-thumb-rounded scrollbar-track-blue-lighter scrollbar-w-2 scrolling-touch"
    >
      <chat-bubble
        v-for="message in this.messages"
        :name="name"
        :key="message.key"
        :message="message"
        :dbMsg="this.messages"
      ></chat-bubble>
    </div>
    <!-- chat bubble end -->

    <!-- message input -->
    <form
      @submit.prevent="sendMessage"
      class="border-t-2 border-gray-200 px-4 pt-4 mb-2 sm:mb-0"
    >
      <div class="relative flex">
        <input
          v-model="inputMessage"
          type="text"
          placeholder="Write your message!"
          class="w-full focus:outline-none focus:placeholder-gray-400 text-gray-600 placeholder-gray-600 pl-12 bg-gray-200 rounded-md py-3"
        />

        <div class="absolute right-0 items-center inset-y-0 hidden sm:flex">
          <button
            type="submit"
            class="inline-flex items-center justify-center rounded-lg px-4 py-3 transition duration-500 ease-in-out text-white bg-blue-500 hover:bg-blue-400 focus:outline-none"
          >
            <span class="font-bold">Send</span>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 20 20"
              fill="currentColor"
              class="h-6 w-6 ml-2 transform rotate-90"
            >
              <path
                d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z"
              ></path>
            </svg>
          </button>
        </div>
      </div>
    </form>
    <!-- message input end-->
  </div>

  <RouterView />
</template>

<style>
.cont-chat {
  height: 70vh;
}
</style>
