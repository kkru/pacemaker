<template>
  <v-text-field
    v-model="message"
    :append-outer-icon="message? 'mdi-send' : ''"
    outlined
    clear-icon="mdi-close-circle"
    clearable
    label="Comment"
    type="text"
    class="commet"
    @click:append-outer="sendMessage"
    @click:clear="clearMessage"
    @keyup.enter="sendMessage"
  ></v-text-field>
</template>

<script>
import { mapGetters } from 'vuex'
import { createComment } from '../api/comment.js'
import { getAchieve, putAchieve } from '~/api/achieve.js'

export default {
  data: () => ({
    message: '',
    nickname: ''
  }),
  mounted() {
    this.nickname = this.$session.get('account').nickname
  },
  computed: {
    ...mapGetters({ getModelRoomId: 'comment/getModelRoomId' })
  },
  methods: {
    async sendMessage() {
      const commentData = {
        nickname: this.nickname,
        modelRoomId: this.getModelRoomId,
        context: this.message
      }
      await createComment(commentData)
        .then(({ data }) => {
          this.$store.dispatch(
            'comment/setCommentList',
            commentData.modelRoomId
          )
        })
        .catch((error) => {
          console.error(error)
        })

      let achieveData = {}
      await getAchieve(this.$session.get('account').id).then(({ data }) => {
        achieveData = data
      })

      achieveData.comment += 1
      await putAchieve(achieveData)

      const achieveModal = this.$store.state.achievement.commentAchieve
      achieveModal.forEach((element) => {
        if (element.number == achieveData.comment) {
          this.$store.commit('modal/setModalData', {
            header: element.name,
            body: '댓글 등록 성공!! \n 업적을 취득하였습니다.',
            img: element.img
          })
          this.$store.commit('achievement/setShowModal', true)
        }
      })
      this.clearMessage()
      let container = document.getElementById('scroll-target')
      container.scrollTop = container.scrollHeight
    },
    clearMessage() {
      this.message = ''
    }
  }
}
</script>
