<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>MORE SWIPERJS</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content class="ion-padding">
      <my-slides
        :options="slideOpts"
        :items="slideData"
        @disable-prev="onDisablePrev"
        @disable-next="onDisableNext"
      ></my-slides>
      <ion-card>
        <ion-card-header><h4>HOME COMPONENT BUTTONS</h4></ion-card-header>
        <ion-card-content>
          <ion-button
            size="small"
            @click="gotoPrevSlide"
            :disabled="disablePrevBtn"
            >PREV SLIDE - EVENT
          </ion-button>
          <ion-button
            size="small"
            @click="gotoNextSlide"
            :disabled="disableNextBtn"
            >NEXT SLIDE - EVENT
          </ion-button>

          <ion-item>
            <ion-button @click="gotoSlide" slot="start">GOTO SLIDE</ion-button>
            <ion-input
              type="number"
              placeholder="Enter Slide Number"
              v-model="slideInput"
            ></ion-input>
          </ion-item>
        </ion-card-content>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
  IonButton,
  IonItem,
  IonInput,
  IonCard,
  IonCardContent,
  IonCardHeader
} from "@ionic/vue";
import { defineComponent, ref, onMounted } from "vue";
import MySlides, { mySlidesEmitter } from "./MySlides.vue";

export default defineComponent({
  name: "Home",
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    MySlides,
    IonButton,
    IonItem,
    IonInput,
      IonCard,
  IonCardContent,
  IonCardHeader
  },
  setup() {
    const disablePrevBtn = ref<boolean>(true);
    const disableNextBtn = ref<boolean>(false);
    const slideInput = ref<number>(0);
    const slideData = ref<any>([
      { name: "one", id: "001" },
      { name: "two", id: "002" },
      { name: "three", id: "003" },
    ]);

    // Optional parameters to pass to the swiper instance.
    // See http://idangero.us/swiper/api/ for valid options.
    const slideOpts = {
      initialSlide: 0,
      speed: 400,
    };

    const gotoNextSlide = () => {
      mySlidesEmitter.emit("next-slide");
    };

    const gotoPrevSlide = () => {
      mySlidesEmitter.emit("prev-slide");
    };

    const gotoSlide = () => {
      const slideIndex = slideInput.value;
      mySlidesEmitter.emit("goto-slide", { slideIndex: slideIndex });
    };

    return {
      // COMPONENTS
      MySlides,
      // PROPERTIES
      slideOpts,
      disablePrevBtn,
      disableNextBtn,
      slideInput,
      slideData,
      // methods
      gotoNextSlide,
      gotoPrevSlide,
      onDisablePrev: ({ value }: { value: any }) => {
        disablePrevBtn.value = value;
      },
      onDisableNext: ({ value }: { value: any }) => {
        disableNextBtn.value = value;
      },
      gotoSlide,
    };
  },
});
</script>

<style scoped>
ion-slide {
  height: 160px;
  background-color: aqua;
}
</style>