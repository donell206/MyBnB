<template lang="pug">
.modal#loginModal
  .modal-content
    h4 Login
    a.btn-floating.close-btn(@click="closeModal"): i.material-icons close
    form.col.s12
      .row
        .input-field
          i.material-icons.prefix account_circle
          input(id="user_email" type="email" v-model="inputs.user_email").validate
          label(for="user_email" data-error="Invalid Email") Email Address
      .row
        .input-field
          i.material-icons.prefix lock
          input(id="user_password" type="password" v-model="inputs.user_password").validate
          label(for="user_password") Password
    span.error-text(v-show="inputs.error") Could not login with those credentials!
  .modal-footer
    button.btn(@click="loginSubmit" type="submit").color-primary submit
      i.material-icons.right send
    button.btn(@click="closeModal").color-primary close
    button.btn-flat(@click="registerPress") register
</template>

<script>
import Server from '../utils/network'

export default {
  data () {
    return {
      state: Store.state,
      msg: 'Main',
      inputs: {
        user_email: '',
        user_password: '',
        error: false
      },
      server: new Server(this)
    }
  },

  methods: {
    loginSubmit () {
      let vm = this
      vm.server.login(this.inputs).then((res) => {
        if (res) {
          vm.server.getUser(res).then((res) => {
            if (res) {
              vm.state.user = res
              vm.$dispatch('loggedIn')
              vm.closeModal()
              return true
            }
          })
        } else {
          vm.inputs.error = true
          return false
        }
      })
    },
    closeModal () {
      this.inputs.user_email = ''
      this.inputs.user_password = ''
      this.inputs.error = false
      $('#loginModal').closeModal()
    },
    registerPress () {
      $('#loginModal').closeModal()
      $('#signUpModal').openModal()
    }
  },

  ready () {
    let vm = this
    $(document).ready(function () {
      $('.loginModalTrigger').leanModal({
        complete: () => {
          vm.closeModal()
        }
      })
    })
  }
}
</script>

<style lang="stylus" scoped>
.modal
  overflow hidden !important
.modal-content
  .close-btn
    background-color black
    position absolute
    top 1em
    right 1em
  form
    margin-top 1.5em
    label
      left 0.1rem !important
  .error-text
    color red
    font-size 1.5em
    opacity 1
    transition opacity 1s ease
.modal-footer
  button
    margin .3em !important
</style>
