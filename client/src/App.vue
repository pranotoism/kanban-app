<template>
  <div class="container">
    <LoginPage v-if="currentPage === 'login'" @changePage="changePage"></LoginPage>
    <RegisterPage v-else-if="currentPage === 'register'" @changePage="changePage"></RegisterPage>
    <MainPage v-else-if="currentPage === 'home'" :allTasks="allTasks" @changePage="changePage" @deleteTask="deleteTask" @editTask="editTask" @patchLeft="patchLeft" @patchRight="patchRight"></MainPage>
    <UpdateForm v-else-if="currentPage === 'update'" :taskData="taskData" @changePage="changePage" @update="update"></UpdateForm>
  </div>
  
</template>

<script>

import LoginPage from './components/LoginPage'
import RegisterPage from './components/Register'
import MainPage from './components/MainPage'
import UpdateForm from './components/UpdateForm'
import axios from 'axios'
import Swal from 'sweetalert2'



export default {
  name: "App",
  data() {
    return {
      message: 'Hello world',
      currentPage: 'login',
      taskData: '',
      coba: '',
      allTasks: [],
    };
  },
  methods: {
    patchLeft(category, id) {
        console.log(category, id);
         axios({
          method: 'patch',
          url: 'https://kanbaban.herokuapp.com/tasks/' + id,
          headers: {
            access_token: localStorage.getItem("access_token")
          },
          data: {
            category
          }
        })
        .then(response => {
          this.fetchTasks()
        })
        .catch(err => {
                console.log(err.response);
                let message = err.response.statusText
                  const Toast = Swal.mixin({
                  toast: true,
                  position: 'top-end',
                  showConfirmButton: false,
                  timer: 3000,
                  timerProgressBar: true,
                  didOpen: (toast) => {
                      toast.addEventListener('mouseenter', Swal.stopTimer)
                      toast.addEventListener('mouseleave', Swal.resumeTimer)
                  }
                })

                Toast.fire({
                    icon: 'error',
                    title: message
                })
          })
    },
    patchRight(category, id) {
        console.log(category, id);
        axios({
          method: 'patch',
          url: 'https://kanbaban.herokuapp.com/tasks/' + id,
          headers: {
            access_token: localStorage.getItem("access_token")
          },
          data: {
            category
          }
        })
        .then(response => {
          this.currentPage = 'home'
          this.fetchTasks()
        })
        .catch(err => {
                console.log(err.response);
                let message = err.response.statusText
                  const Toast = Swal.mixin({
                  toast: true,
                  position: 'top-end',
                  showConfirmButton: false,
                  timer: 3000,
                  timerProgressBar: true,
                  didOpen: (toast) => {
                      toast.addEventListener('mouseenter', Swal.stopTimer)
                      toast.addEventListener('mouseleave', Swal.resumeTimer)
                  }
                })

                Toast.fire({
                    icon: 'error',
                    title: message
                })
          })
    },
    changePage(page) {
      this.currentPage = page
    },
    deleteTask(id) {
      this.deletes(id)
    },
    deletes(id) {
      console.log(id);
      axios({
          method: 'delete',
          url: 'https://kanbaban.herokuapp.com/tasks/' + id,
          headers: {
            access_token: localStorage.getItem("access_token")
          }
        })
        .then(response => {
          this.allTasks = response
          location.reload()
        })
        .catch(err => {
                console.log(err.response);
                let message = err.response.statusText
                Swal.fire({
                    title: message,
                    icon: 'error',
                
                })
        })
    },
    reload() {
      this.$forceUpdate();
    },
    editTask(task) {
      this.taskData = task
    },
    fetchTasks() {
      axios({
          method: 'get',
          url: 'https://kanbaban.herokuapp.com/tasks',
          headers: {
            access_token: localStorage.getItem("access_token")
          }
        })
        .then(response => {
          let data = response.data.data
          this.allTasks = data;
          
        })
    },
    update(title, category, id) {
      axios({
          method: 'put',
          url: 'https://kanbaban.herokuapp.com/tasks/' + id,
          headers: {
            access_token: localStorage.getItem("access_token")
          },
          data: {
            title,
            category
          }
        })
        .then(response => {
          const Toast = Swal.mixin({
            toast: true,
            position: 'top-end',
            showConfirmButton: false,
            timer: 3000,
            timerProgressBar: true,
            didOpen: (toast) => {
                toast.addEventListener('mouseenter', Swal.stopTimer)
                toast.addEventListener('mouseleave', Swal.resumeTimer)
            }
          })

          Toast.fire({
              icon: 'success',
              title: 'A task has been updated successfully'
          })
          this.currentPage = 'home'
        })
        .catch(err => {
                console.log(err.response);
                let message = err.response.statusText
                Swal.fire({
                    title: message,
                    icon: 'error',
                
                })
          })
    }
  },
  components: {
    LoginPage,
    RegisterPage,
    MainPage,
    UpdateForm
  },
  created() {
    if (localStorage.getItem('access_token')) {
      this.currentPage = 'home'
    } else {
      this.currentPage = 'login'
    }
  }
};
</script>

<style scoped>

</style>