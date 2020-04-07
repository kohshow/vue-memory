<template>
    <div
        :class="cardClasses"
        @click="tap"
        :style="cardTransform"
    >
        <div class="card_inner">
            <div class="card_front" />
            <div
                :data-matchkey="card.matchkey"
                class="card_back"
            >
                <img :src="require(`${card.imgUrl}`)" class="card_img">
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Card',
    props: {
        card: {
            type: Object,
            default: () => {},
        },
    },

    computed: {
        cardClasses() {
            return {
                'card': true,
                'card-flipped': this.card.flipped,
                'card-matched': this.card.matched,
            };
        },
    },

    methods: {
        tap() {
            this.$emit('tapped', this.card.id);
        },
    },
};
</script>

<style>
* {
  box-sizing: border-box;
}
.text {
  color: orange;
}
.card {
  width: auto;
  max-width: 100%;
  display: block;
  perspective: 1000px;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}
.card_inner {
  box-shadow: 0 0 30px rgba(0, 0, 0, 0.1);
  transition: 0.6s;
  transform-style: preserve-3d;
  position: relative;
}
.card-flipped .card_inner {
  transform: rotateY(180deg);
}
.card-matched .card_inner {
  opacity: 0.2;
}
.card_inner,
.card_front,
.card_back {
  border-radius: 5px;
  width: 100%;
  height: 100%;
}
.card_back {
  align-items: center;
  display: flex;
  justify-content: center;
}
.card_img {
  display: block;
  height: auto;
  max-width: 100%;
  max-height: 100%;
}
.card * {
  pointer-events: none;
}
.card_front,
.card_back {
  backface-visibility: hidden;
  -webkit-backface-visibility: hidden;
  position: absolute;
  background-size: contain;
  background-repeat: no-repeat;
  background-position: 50% 50%;
  background-color: #fff;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.card_front {
  background: linear-gradient(45deg, #222, rgb(200, 39, 92));
  z-index: 2;
}
.card_back {
  transform: rotateY(180deg);
}
</style>