<template>
  <authenticator>
    <template v-slot="{ signOut }">
      <ion-button @click="signOut">サインアウト</ion-button>
      <div id="container">
        <strong>{{ name }}</strong>
        <p>Explore <a target="_blank" rel="noopener noreferrer" href="https://ionicframework.com/docs/components">UI Components</a></p>
      </div>
      <div id="app">
        <h1>Todo App</h1>
        <input type="text" v-model="taskName" placeholder="Todo name">
        <button @click="createTodos">Create Todo</button>
    </div>
    </template>
  </authenticator>
</template>

<script lang="ts">
  import { API } from 'aws-amplify';
import { createTodo } from '../graphql/mutations';

import { defineComponent, ref, reactive } from 'vue';
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
    const taskName = ref('')
    const createTodos = async() => {
      console.log(taskName.value)
      if(!taskName.value) return 
      await API.graphql({
        query: createTodo,
        variables: {input: {'name': taskName.value}},
      });
      taskName.value = ''
    }
    return {
      taskName,
      createTodos,
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
