<template>
  <nav class="navbar">
    <div class="container">
      <div class="navbar-brand">
        <router-link class="navbar-item" @click="menu = false" to="/"><img id="logo-pic" src="/assets/logo.png"><strong id="logo-text">Pixelect</strong></router-link>

        <div :class="`navbar-burger${ menu ? ' is-active' : '' }`" @click="menu = !menu">
          <span></span>
          <span></span>
          <span></span>
        </div>
      </div>

      <div :class="`navbar-menu${ menu ? ' is-active' : '' }`">

        <div class="navbar-end">

          <div class="navbar-item">
            <div class="field is-grouped">
              <p v-for="button in buttons" class="control">
                <button :class="button.class" @click="bus.$emit(button.action)">
                  <img :src="button.imgUrl"/>
                  {{ button.text }}
                </button>
              </p>
            </div>
          </div>

          <div
            :class="{ 'navbar-item': true, 'has-dropdown': true, 'is-active': dropdownVisible }"
            @click="dropdownVisible = !dropdownVisible"
          >
            <a class="navbar-link plain-navbar-dropdown">
              {{userName}}
            </a>
            <div class="navbar-dropdown is-right">
              <a class="navbar-item" @click="logout">
                Logout
              </a>
            </div>
          </div>

        </div>
      </div>
    </div>
  </nav>
</template>

<script>
import { mapState } from 'vuex'
import store from '@/store'
import bus from '@/bus'

export default {
  data() {
    return {
      menu: false,
      dropdownVisible: false,
      bus,
    }
  },
  computed: mapState({
    userName: state => state.userName,
    buttons(state) {
      switch (this.$route.name) {
        case 'index':
          return [
            {
              text: 'Create Group',
              class: 'button is-primary',
              action: 'createGroup',
            },
          ]
        case 'group/groupName':
        case 'group':
          return state.isGroupOwner
            ? [
                {
                  text: 'Publish',
                  class: 'button is-primary publish',
                  action: 'publish',
                  imgUrl: '/assets/FB_logo.png',
                },
                {
                  text: 'Invite',
                  class: 'button is-primary',
                  action: 'invite',
                  imgUrl: '',
                },
              ]
            : [
                {
                  text: 'Invite',
                  class: 'button is-primary',
                  action: 'invite',
                  imgUrl: '',
                },
              ]
      }
    },
  }),
  methods: {
    logout: function() {
      FB.logout(response => {
        this.$router.push('/')
        window.location.reload()
      })
    },
  },
}
</script>

<style scoped>
.navbar {
  box-shadow: rgba(10, 10, 10, 0.2) 0 0 10px;
  z-index: 1000; /* loader has z-index: 999; */
}

/* We can photoshop our own Logo + Text if we don't like these */
#logo-text {
  padding-left: 0.60rem;
  font-size: 1.4rem;
}

#logo-pic {
  height: 2.3rem;
  width: 2.3rem;
  max-height: 2.5rem;
}

@media screen and (min-width: 1008px) {
  .plain-navbar-dropdown::after {
    right: 1.40rem;
  }
}

.publish img {
  height: 1.2rem;
  margin-right: 0.6rem;
}
</style>
