<template>
  <authenticator>
    <template v-slot="{ signOut }">
      <ion-button @click="signOut">サインアウト</ion-button>
      <div id="container">
        <strong>{{ name }}</strong>
        <p>Explore <a target="_blank" rel="noopener noreferrer" href="https://ionicframework.com/docs/components">UI Components</a></p>
      </div>
      <div id="app">
        <div>
          <h1>Todo App</h1>
          <input type="text" v-model="taskName" placeholder="Todo name">
          <button @click="addTask">Create Todo</button>
        </div>
        <div>
          <h1>Chat App</h1>
          <input type="text" v-model="content" placeholder="Todo name">
          <button @click="addContent">送信</button>
          <ul>
            <li v-for="message in messages" :key="message?.id">
              {{ message.content }}
            </li>
          </ul>
        </div>
      </div>
    </template>
  </authenticator>
</template>

<script lang="ts">
import { API, graphqlOperation } from 'aws-amplify';
import { createTodo, createChat } from '../graphql/mutations';
import { listChats } from '../graphql/queries'
import { onCreateChat } from '../graphql/subscriptions'
import { ListChatsQuery, OnCreateChatSubscription, Chat } from '../API'
import { defineComponent, onMounted, ref, watch } from 'vue';
import { Authenticator } from "@aws-amplify/ui-vue";
import { IonButton } from '@ionic/vue';
import "@aws-amplify/ui-vue/styles.css";

export default defineComponent({
  name: 'ExploreContainer',
  props: {
    name: String
  },
  components: {
    Authenticator,
    IonButton
  },
  setup () {
    type ChatSubscriptionEvent = { value: { data: OnCreateChatSubscription } };
    const taskName = ref('')
    const addTask = async() => {
      console.log(taskName.value)
      if(!taskName.value) return 
      await API.graphql({
        query: createTodo,
        variables: {input: {'name': taskName.value}},
      });
      taskName.value = ''
    }
    const content = ref('')
    const addContent = async() => {
      if(!content.value) return
      await API.graphql({
        query: createChat,
        variables: {input: {content: content.value}}
      })
      content.value = ''
    }
    const messages = ref<Chat[]>()
    onMounted(async() => {
      const chatMessage = await API.graphql(graphqlOperation(listChats));
      if ('data' in chatMessage && chatMessage.data) {
        const chats = chatMessage.data as ListChatsQuery
        if (chats.listChats) {
          messages.value = chats.listChats.items as Chat[]
        }
      }
      console.log(chatMessage)
    }),

    (() => {
      const client = API.graphql({
        query: onCreateChat,
        authMode: 'API_KEY',
      })
      if ("subscribe" in client) {
        client.subscribe({
          next: ({ value: { data } }: ChatSubscriptionEvent) => {
            if (data.onCreateChat) {
              const chat: Chat = data.onCreateChat;
              messages.value?.push(chat)
            }
          },
          error: (error: any) => console.warn(error)
        });
      }
    })()
    return {
      taskName,
      addTask,
      content,
      addContent,
      messages
    }
  }
});
</script>

<style scoped>
#container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  color: #8c8c8c;
  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
