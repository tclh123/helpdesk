<template>
  <a-layout-header>

    <a-menu
      theme="dark"
      mode="horizontal"
      :style="{ lineHeight: '64px' }"
      :selectedKeys = "selectedMenu"
    >
      <a-menu-item key="1">
        <router-link :to="{name: 'Home'}">Helpdesk</router-link>
      </a-menu-item>
      <a-menu-item key="2">
        <router-link :to="{name: 'HTicketList'}">Tickets</router-link>
      </a-menu-item>
      <a-sub-menu :style="{ float: 'right' }">
        <span slot="title">
            <span>{{ user.display_name }}</span>
            <a-avatar shape="square" icon="user"
                      :src="user.avatar"
            />
        </span>
        <a-menu-item key="3">
          <a href="/api/user/me">
          <!-- TODO a user profile page is needed -->
            <i class="glyphicon glyphicon-user"></i>
            whoami
          </a>
        </a-menu-item>
        <a-menu-item key="4" v-on:click="logout">
          logout
        </a-menu-item>
      </a-sub-menu>
    </a-menu>

  </a-layout-header>
</template>

<script>
import {HRequest} from '../utils/HRequests'
export default {
  name: 'HHeader',
  computed: {
    user () {
      return this.$store.state.userProfile
    },
    selectedMenu () {
      let ticketRouteNames = ['HTicketList', 'HTicketDetail']
      if (ticketRouteNames.includes(this.$route.name)) {
        return ['2']
      } else return ['1']
    }
  },
  mounted () {
    this.updateUserProfile()
  },
  methods: {
    updateUserProfile () {
      if (!this.$store.getters.isAuthenticated) {
        HRequest.get('/api/user/me').then(
          (response) => {
            if (response.data.data.is_authenticated === true) {
              this.$store.dispatch('updateUserProfile', response.data.data)
            } else {
              this.$router.push({name: 'Login'})
            }
          }
        ).catch()
      }
    },
    logout () {
      HRequest.post('/auth/logout').then(() => {
        document.cookie = 'session' + '=; expires=Thu, 01 Jan 1970 00:00:01 GMT;'
        this.$router.push({name: 'Login'})
      })
    }
  }
}
</script>

<style scoped>

</style>
