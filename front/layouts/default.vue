<template>
  <v-app dark>
    <v-btn class="menu-icon" fab color="white" v-if="!drawer" @click="menuBtn">
      <v-icon>mdi-format-list-bulleted-square</v-icon>
    </v-btn>
    <v-navigation-drawer v-model="drawer" clipped fixed app>
      <v-container>
        <v-flex class="headline" ma-3>
          <nuxt-link to="/">
            <img src="/achieve/maker04.svg" id="mainIcon">
            <span id="mainTitle">{{ title }}</span>
          </nuxt-link>
        </v-flex>
        <!-- <v-btn text nuxt to="/MemberInfoPage">마이 페이지</v-btn>
        <v-btn text @click="logout">로그아웃</v-btn> -->
      </v-container>
      <v-list>
        <div  v-for="(item, i) in items" :key="i">
          <div v-if="item.event">
            <v-list-item :to="item.to" router exact @click="logout">
              <v-list-item-action>
                <v-icon>{{ item.icon }}</v-icon>
              </v-list-item-action>
              <v-list-item-content>
                <v-list-item-title v-text="item.title"/>
              </v-list-item-content>
            </v-list-item>
          </div>
          <div v-else>
            <v-list-item :to="item.to" router exact>
              <v-list-item-action>
                <v-icon>{{ item.icon }}</v-icon>
              </v-list-item-action>
              <v-list-item-content>
                <v-list-item-title v-text="item.title"/>
              </v-list-item-content>
            </v-list-item>
          </div>
        </div>
      </v-list>
    </v-navigation-drawer>
    <v-content class="mx-6">
      <Modal v-if="this.$store.state.achievement.showModal" @close="modalClose">
        <h3 slot="header">
          {{this.$store.state.modal.header}}
          <span
            class="fas fa-times closeModalBtn"
            @click="modalClose"
          >
            <v-icon>mdi-close</v-icon>
          </span>
        </h3>
        <div slot="body">
          <img class="modalImg" :src="this.$store.state.modal.img" />
          <pre class="modalBodyText">{{this.$store.state.modal.body}}</pre>
        </div>
      </Modal>
      <nuxt />
    </v-content>
  </v-app>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import Modal from '~/components/Modal.vue';

export default {
  data() {
    return {
      user: '',
      drawer: null,
      items: [
        {
          icon: 'mdi-home',
          title: '홈',
          to: '/MainPage'
        },
        {
          icon: 'mdi-account',
          title: '마이 페이지',
          to: '/MemberInfoPage'
        },
        {
          icon: 'mdi-apps',
          title: '방 생성',
          to: '/RoomAdd'
        },
        {
          icon: 'mdi-logout',
          title: '로그아웃',
          to: '/',
          event: 'logout'
        }
      ],
      title: 'PaceMaker'
    }
  },
  components: {
    Modal
  },
  mounted() {
    this.user = this.$session.get("account")
  },
  computed: {
    ...mapGetters({
      getUserEmail: 'user/getUserEmail',
      getUserName: 'user/getUserName',
      getUserIcon: 'user/getUserIcon'
    }),
  },
  methods: {
    logout() {
      this.$session.remove('account')
      this.$storage.setUniversal('isAuth', false)
      this.$router.push('/')
    },
    menuBtn() {
      this.drawer = !this.drawer
    },
    modalClose() {
      this.$store.commit('achievement/setShowModal',false);
    }
  }
}
</script>

<style src="../assets/color.css"></style>

<style scoped>
#mainIcon{
  width: 60px;
  position: absolute;
  left: 8px;
  top: 19px;
}
#mainTitle{
  position: relative;
  left: 50px;
  top: 8px;
  font-size: 28px;
  font-weight: 500;
  text-shadow: 0px 0px 6px #6bb5d2;
}
.menu-icon {
  width: 40px;
  height: 40px;
  z-index: 80;
  margin: 13px 0 0 11px;
  font-size: 50px;
  position: fixed;
  border-radius: 50px;
}
a {
  text-decoration: none;
  color: black;
}
.modalImg {
  width: 220px;
  margin-bottom: 20px;
}
.closeModalBtn {
  position: absolute;
  right: 10px;
  top: 10px;
  cursor: pointer;
}
.modalBodyText {
  font-weight: bold;
}
</style>
