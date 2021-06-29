<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Blank</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Blank</ion-title>
        </ion-toolbar>
      </ion-header>

      <div id="container">
        <pre>{{ count }}</pre>
        <ion-button @click="sendNotification">Send notification</ion-button>
        <ion-button @click="clearBadge">Clear badge</ion-button>
        <ion-button @click="removeCount">Less count</ion-button>
        <ion-button @click="addCount">More count</ion-button>
      </div>
    </ion-content>
  </ion-page>
</template>

<script>
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonButton } from '@ionic/vue'
import { defineComponent, onMounted, ref } from 'vue'
import { LocalNotifications } from '@capacitor/local-notifications'
import { Badge } from '@robingenz/capacitor-badge'

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton
  },
  setup () {
    const count = ref(0)
    const initNotifications = async () => {
      const isEnable = await LocalNotifications.areEnabled()
      if (!isEnable) {
        await LocalNotifications.requestPermission()
      }

      const isBadgeEnable = await Badge.checkPermissions()
      if (!isBadgeEnable) {
        await Badge.requestPermissions()
      }
    }

    const sendNotification = async () => {
      const notifs = await LocalNotifications.schedule({
        notifications: [
          {
            title: "Messages non lus",
            body: `Vous avez 15 messages non lus`,
            id: 1,
            iconColor: '#ff00ff',
            vibration: true
          }
        ]
      })
      await Badge.set({ count: count.value })
      // logs the notifcations
      console.log('scheduled notifications', notifs)
    }

    const addCount = () => {
      count.value++
    }

    const removeCount = () => {
      count.value--
    }

    const clearBadge =  async () => {
      await Badge.clear()
    }
    onMounted(initNotifications)
    return {
      count,
      removeCount,
      addCount,
      clearBadge,
      sendNotification
    }
  }
})
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
