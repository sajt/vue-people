<template>
  <div class="left-side">
    <nuxt-child
      v-if="!showRootContent"
      class="full-height" />
    <div
      v-if="showRootContent"
      class="intro-text pa-4"
    >
      <img
        src="~/assets/images/logo-vuepeople.svg"
        alt="VuePeople logo"
        class="logo"
      >

      <h4 class="subheading mb-3">
        Welcome to VuePeople!
      </h4>

      <p>
        VuePeople.org is a network that connects the Vue.JS community with events<br>
        and work opportunities.
      </p>

      <p>
        VuePeople.org is open sourced.<br>
        You can contribute or submit a ticket at
        <a
          href="https://github.com/pulilab/vue-people"
          target="_blank">https://github.com/pulilab/vue-people
        </a>
      </p>

      <v-btn
        v-show="showGoToMapButton"
        :disabled="!mapReady"
        block
        color="primary"
        class="btn-gotomap"
        @click="setGoToMap(true)"
      >
        <span v-show="mapReady"> Go To Map </span>
        <v-icon
          v-show="mapReady"
          class="ml-1"
        >
          mdi-arrow-right
        </v-icon>

        <span v-show="!mapReady"> Loading Map </span>
        <v-progress-circular
          v-show="!mapReady"
          :size="26"
          class="ml-2"
          indeterminate
        />
      </v-btn>
    </div>

    <div
      v-show="mapReady"
      class="new-user-pop px-4 pb-4"
    >
      <h6 class="body-2 mb-4">
        Welcome to our latest member:
      </h6>
      <v-card
        v-if="lastUser"
        :class="['up-wrapper', 'elevation-4', 'animated', lastUserAnimation]"
        light
        @click.native.stop.capture="goToLatestUserDetails"
      >
        <user-avatar :id="lastUser" />
      </v-card>
    </div>

    <div class="credit elevation-6">
      <span>
        <img
          src="~/assets/images/logo-pulilab.svg"
          alt="Pulilab logo"
          class="logo"
        >
      </span>
      <span>Created with love by <a href="http://pulilab.com">Pulilab</a></span>
    </div>
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex';
import UserAvatar from './UserAvatar';
export default {
  name: 'LeftSide',
  components: {
    UserAvatar
  },
  data () {
    return {
      lastUser: null,
      lastUserAnimation: null
    };
  },
  computed: {
    ...mapGetters({
      mapReady: 'map/getMapReady',
      latestUser: 'people/getLatestUser'
    }),
    showRootContent () {
      const leftRoutes = ['index-user-id', 'index-meetup-id'];
      return this.$route && !leftRoutes.includes(this.$route.name);
    },
    showGoToMapButton () {
      if (!this.$mq) {
        return this.$device.isMobileOrTablet;
      }
      return this.$mq === 'sm' || this.$mq === 'xs';
    }
  },
  watch: {
    latestUser: {
      immediate: true,
      handler (user, old) {
        if (user && !old) {
          this.lastUser = user;
          this.lastUserAnimation = 'bounceInLeft';
        } else if (user && old) {
          this.lastUserAnimation = 'bounceOutDown';
          setTimeout(() => {
            this.lastUser = user;
            this.lastUserAnimation = 'bounceInLeft';
          }, 1000);
        }
      }
    }
  },
  methods: {
    ...mapActions({
      setGoToMap: 'setGoToMap'
    }),
    goToLatestUserDetails () {
      this.$router.push(`/user/${this.latestUser}/`);
    }
  }
};
</script>

<style lang="less">
  @import "../assets/style/variables.less";
  @import "../assets/style/mixins.less";

  .left-side {
    position: relative;
    overflow-x: hidden;
    overflow-y: hidden;
    height: 100vh;
    background-color: @color-white;

    .intro-text {
      max-height: calc(100% - 64px);
      overflow-y: auto;

      .logo {
        width: auto;
        height: 56px;
        margin-bottom: 40px;
      }

      h4 {
        color: @font-dark-primary;
      }

      p {
        margin-bottom: 24px;
        color: @font-dark-secondary;

        a {
          white-space: nowrap;
        }
      }

      .btn-gotomap {
        float: left;
        margin: 8px 0 48px;

        &.btn--disabled  {
          .btn__content {
            background-color: fade(@color-brand-primary, 70%);
            color: @color-white;
          }
        }
      }
    }

    .new-user-pop {
      h6 {
        color: @font-dark-secondary;
      }

      .up-wrapper {
        cursor: pointer;
        padding: 12px;
      }
    }

    .credit {
      z-index: 10;
      position: fixed;
      bottom: 0;
      left: 0;
      width: 320px;
      height: 64px;
      display: flex;
      align-items: center;
      padding: 14px 24px 14px 20px;
      background-color: @color-white;
      color: @font-dark-secondary;

      > span {
        height: 36px;
        line-height: 36px;
      }

      .logo {
        width: 36px;
        height: 36px;
        margin-right: 12px;
      }
    }

    // Responsive
    .viewport-sm & {
      // elevation 8
      box-shadow: 0px 5px 5px -3px rgba(0, 0, 0, .2), 0px 8px 10px 1px rgba(0, 0, 0, .14), 0px 3px 14px 2px rgba(0, 0, 0, .12) !important;
    }
  }
</style>
