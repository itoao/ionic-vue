<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tab だよ!</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tab 1</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <ExploreContainer name="Tab 1 page" />
      <ion-grid>
        <ion-row>
          <ion-col size-xs="3">
            <div>1 of 3</div>
          </ion-col>
          <ion-col size-xs="3">
            <div>2 of 3</div>
          </ion-col>
          <ion-col size-xs="3">
            <div>3 of 3</div>
          </ion-col>
        </ion-row>
      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonGrid, IonRow, IonCol } from '@ionic/vue';
import ExploreContainer from '@/components/ExploreContainer.vue';

export default  defineComponent({
  name: 'Tab1Page',
  components: { ExploreContainer, IonHeader, IonToolbar, IonTitle, IonContent, IonPage, IonGrid, IonRow, IonCol }
});
</script>
