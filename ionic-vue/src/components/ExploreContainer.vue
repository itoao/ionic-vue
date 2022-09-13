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
            <li v-for="message in messages" :key="message.id">
              {{message.value}}
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
import { getChat } from '../graphql/queries'
import { onCreateChat } from '../graphql/subscriptions'
import { Chat } from '../API'
import { defineComponent, onMounted, ref, watch } from 'vue';
import { Authenticator } from "@aws-amplify/ui-vue";
import { IonButton } from '@ionic/vue';
import "@aws-amplify/ui-vue/styles.css";
import Observable from 'zen-observable-ts';
import { getHeapCodeStatistics } from 'v8';
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
    const message = ref('')
    onMounted(async() => {
      const chatMessage = await API.graphql({
        query: getChat,
        variables: {id: 'f4d8f6ba-91cd-4237-9def-87192344fbb6'}
      })
      console.log(chatMessage)
      message.value = chatMessage.data?.getChat.content
    })
    // watch(() => {
    //   const subscription = await API.graphql(
    //    graphqlOperation(onCreateChat)
    //   ).subscribe({
    //    next: (contentData: string) => {
    //      console.log(contentData)
    //    }
    //   }) as Observable<any>
    // })
    return {
      taskName,
      addTask,
      content,
      addContent

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
