<template>
  <div class="col-sm-10 col-sm-offset-1">
    <div class="alert alert-danger errorList" v-if="errors">
      <div v-for="error in errors" role="alert">{{ error }}</div>
    </div>
    <h3>Edit Task - {{$route.params.id}}</h3>
    <form v-on:submit.prevent="update">
      <div class="form-group">
        <label for="task_name">Task Name</label>
        <input v-model="task.name" type="text" class="form-control" id="task_name" placeholder="Task">
      </div>
      <button type="submit" class="btn btn-primary edit-button">Submit</button>
    </form>
  </div>
</template>
<style lang="sass" scoped>
  .errorList
    margin-top: 25px
</style>
<script>
  import AppHeader from '../template/AppHeader'
  import AppFooter from '../template/AppFooter'
  import Utils from '../../utils'
  import NProgress from 'nprogress'
  import swal from 'sweetalert'

  export default {
    ready () {
      this.fetchTask()
    },

    data () {
      return {
        task: {},
        errors: null
      }
    },

    methods: {
      update () {
        NProgress.start()
        window.$('.edit-button').button('loading')
        this.$http.post(this.$root.serverUrl + 'task/update/' + this.$route.params.id, { name: this.task.name })
          .then((response) => {
            NProgress.done()
            this.task = {}
            swal('Your task is updated successfully')
            this.$router.go('/task')
          })
          .catch((response) => {
            this.errors = Utils.getErrorArray(response.data.errors)
            NProgress.done()
            window.$('.edit-button').button('reset')
          })
      },

      fetchTask () {
        NProgress.start()
        this.$http.post(this.$root.serverUrl + 'task/edit/' + this.$route.params.id, {})
          .then((response) => {
            window.$('.edit-button').button('reset')
            NProgress.done()
            this.task = response.data
          })
          .catch((response) => {
            NProgress.done()
            window.$('.edit-button').button('reset')
          })
      }
    },

    components: {
      AppHeader, AppFooter
    }
  }
</script>
