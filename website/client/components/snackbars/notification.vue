<template lang="pug">
transition(name="fade")
  .notification.callout.animated(:class="classes", v-if='show')
    .row(v-if='notification.type !== "info" && notification.type !== "drop"')
      .text.col-7.offset-1
        div
          | {{message}}
      .icon.col-4
        div.svg-icon(v-html="icons.health", v-if='notification.type === "hp"')
        div.svg-icon(v-html="icons.gold", v-if='notification.type === "gp"')
        div.svg-icon(v-html="icons.star", v-if='notification.type === "xp"')
        div.svg-icon(v-html="icons.mana", v-if='notification.type === "mp"')
        div(v-html='notification.text')
    .row(v-if='notification.type === "info"')
      .text.col-12
        div(v-html='notification.text')
    .row(v-if='notification.type !== "info" && notification.type === "drop"')
      .col-2
        .icon-item
          div(:class='notification.icon')
      .text.col-9
        div(v-html='notification.text')
</template>

<style lang="scss" scoped>
  .notification {
    border-radius: 1000px;
    background-color: #24cc8f;
    box-shadow: 0 2px 2px 0 rgba(26, 24, 29, 0.16), 0 1px 4px 0 rgba(26, 24, 29, 0.12);
    color: white;
    width: 300px;
    margin-left: 1em;
    margin-bottom: 1em;
  }

  .info {
    max-height: 56px;
    background-color: #46a7d9;
    padding-top: .5em;
  }

  .negative {
    background-color: #f74e52;
  }

  .text {
    text-align: center;
    padding: .5em;
  }

  .svg-icon {
    width: 20px;
    margin-right: .5em;
  }

  .hp .icon {
    color: #f74e52;
  }

  .mp .icon {
    color: #2995cd;
  }

  .icon {
    background: #fff;
    color: #ffa623;
    border-radius: 0 1000px 1000px 0;
    padding: .5em;

    div {
      display: inline-block;
      vertical-align: bottom;
    }
  }

  .drop {
    background-color: #4e4a57;
  }

  .icon-item {
    width: 64px;
    height: 64px;
    background-color: #ffffff;
    box-shadow: 0 2px 2px 0 rgba(26, 24, 29, 0.16), 0 1px 4px 0 rgba(26, 24, 29, 0.12);
    border-radius: 50%;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s
  }

  .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0
  }
</style>

<script>
import health from 'assets/svg/health.svg';
import gold from 'assets/svg/gold.svg';
import star from 'assets/svg/star.svg';
import mana from 'assets/svg/mana.svg';

export default {
  props: ['notification'],
  data () {
    return {
      timer: null,
      icons: Object.freeze({
        health,
        gold,
        star,
        mana,
      }),
      show: true,
    };
  },
  created () {
    let timeout = this.notification.hasOwnProperty('timeout') ? this.notification.timeout : true;
    if (timeout) {
      let delay = this.notification.delay || 1000;
      delay += this.$store.state.notificationStore.length * 1000;
      setTimeout(() => {
        this.show = false;
      }, delay);
    }
  },
  watch: {
    show () {
      setTimeout(() => {
        this.$store.state.notificationStore.splice(0, 1);
      }, 1000);
    },
  },
  computed: {
    message () {
      let direction = this.negative === 'negative' ? 'lost' : 'gained';

      if (this.notification.type === 'hp') return `You ${direction} some ${this.$t('health')}`;
      if (this.notification.type === 'mp') return `You ${direction} some ${this.$t('mana')}`;
      if (this.notification.type === 'xp') return `You ${direction} some exp`;
      if (this.notification.type === 'gp') return `You ${direction} some ${this.$t('gold')}`;
    },
    negative () {
      return this.notification.sign === '-' ? 'negative' : 'positive';
    },
    classes () {
      return `${this.notification.type} ${this.negative}`;
    },
  },
};
</script>
