<template>
  <ion-card>
    <ion-card-header><h4>MY SLIDES COMPONENT</h4></ion-card-header>
    <ion-slides
      pager="true"
      :options="slideOpts"
      ref="mySlides"
      @ionSlideDidChange="slideChanged"
    >
      <ion-slide v-for="item in items" :key="item.id">
        {{ item }}
      </ion-slide>
    </ion-slides>
    <div :style="{ textAlign: 'center', paddingTop: '16px' }">
      <ion-button @click="prevSlide" :disabled="disablePrevBtn"
        >prev</ion-button
      >
      <ion-button @click="nextSlide" :disabled="disableNextBtn"
        >next</ion-button
      >
    </div>
  </ion-card>
</template>

<script lang="ts">
import {
  IonButton,
  IonSlides,
  IonSlide,
  IonCard,
  IonCardHeader,
} from "@ionic/vue";
import { defineComponent, ref, onMounted } from "vue";
import { TinyEmitter } from "tiny-emitter";

export const mySlidesEmitter = new TinyEmitter();

export default defineComponent({
  name: "MySlides",
  components: {
    IonSlides,
    IonSlide,
    IonButton,
    IonCard,
    IonCardHeader,
  },
  props: ["slideOpts", "items"],
  setup(props, ctx) {
    const mySlides = ref<any>(null);
    const disablePrevBtn = ref<boolean>(true);
    const disableNextBtn = ref<boolean>(false);

    // Optional parameters to pass to the swiper instance.
    // See http://idangero.us/swiper/api/ for valid options.
    const slideOpts = {
      initialSlide: 0,
      speed: 400,
    };

    // when slide changes, enable and disable appropriate buttons
    const slideChanged = async () => {
      console.log("slideChanged");
      const s = await mySlides?.value?.$el.getSwiper();

      const slideLength = s.slides.length;
      const activeSlide = s.activeIndex;

      disablePrevBtn.value = activeSlide === 0;
      disableNextBtn.value = activeSlide === slideLength - 1;
      ctx.emit("disable-prev", { value: disablePrevBtn.value });
      ctx.emit("disable-next", { value: disableNextBtn.value });
    };

    const nextSlide = async () => {
      const s = await mySlides?.value?.$el.getSwiper();
      await s.slideNext();
    };

    const prevSlide = async () => {
      const s = await mySlides?.value?.$el.getSwiper();
      await s.slidePrev();
    };

    onMounted(() => {
      // emit initial values
      ctx.emit("diasable-prev", { value: disablePrevBtn.value });
      ctx.emit("diasable-next", { value: disableNextBtn.value });

      mySlidesEmitter.on(
        "goto-slide",
        async ({ slideIndex }: { slideIndex: number }) => {
          try {
            const s = await mySlides?.value?.$el.getSwiper();
            s.slideTo(slideIndex);
          } catch (e) {
            console.log(e);
          }
        }
      );

      mySlidesEmitter.on("next-slide", (x: any) => {
        nextSlide();
      });

      mySlidesEmitter.on("prev-slide", (x: any) => {
        prevSlide();
      });
    });
    return {
      // properties
      mySlides,
      disablePrevBtn,
      disableNextBtn,
      // functions
      nextSlide,
      prevSlide,
      slideChanged,
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