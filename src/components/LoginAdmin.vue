<template>

  <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-6 col-xl-4 form-box mt-5">

                <!-- CUSTOMER LOGIN FORM -->
                <form novalidate autocomplete="off" class="p-3 mt-3 mb-3 text-left" @submit.prevent="send()">
                    <h4 class="text-black text-left ml-3">Iniciar Sesión</h4>
                    <hr>
                    <div v-if="error" class="alert alert-danger">
                        Los datos ingresados son incorrectos.
                    </div>
                    <div class="form-group col-12">
                        <label for="email">Correo electrónico</label>
                        <input type="text" id="email" class="form-control" v-model="$v.f.email.$model">
                        <div v-if="$v.f.email.$error && $v.f.email.$dirty" class="alert alert-danger mt-1">
                            <div v-if="$v.f.email.required.$invalid">- Este campo es requerido</div>
                            <div v-if="$v.f.email.email.$invalid">- Debe ser un email válido</div>
                        </div>
                    </div>
                    <div class="form-group  col-12">
                        <label for="password">Contraseña</label>
                        <input type="password" id="password" class="form-control" v-model="$v.f.password.$model">
                        <div v-if="$v.f.password.$error && $v.f.password.$dirty" class="alert alert-danger mt-1">
                            <div v-if="$v.f.password.required.$invalid">- Este campo es requerido</div>

                        </div>
                    </div>
                    <div class="form-group col-4">
                        <input type="submit" :disabled="false" class="btn btn-success mt-4" value="Enviar">
                    </div>

                </form>
                
                <br>
                
            </div>
        </div>
    </div>

</template>

<script>
  import {required,email} from '@vuelidate/validators'
  
  export default  {
    name: 'src-components-login',
    props: [],
    mounted () {
       if (this.$store.state.isLoggedAdmin) {
            this.$router.push('/home-adm')
        }
        
        this.resetForm()
    },
    data () {
      return {
        f: this.resetForm(),
        url: 'http://localhost:3000/api/admins/login',
      }
    },
    validations: {
        f: {
            email: {
                required,
                email
            },
            password: {
                required
            }
        }
    },
    methods: {    
      sendDataFormAxios(data) {
            this.axios.post(this.url, data, {
                    'content-type': 'application/json'
                })
                .then(res => {
                    if (res.data) {
                        //let userCompleto = res.data.user
                        //let userCompletoSTR = JSON.stringify(userCompleto)
                        //sessionStorage.setItem('admin', res.data.username)
                        //this.$store.dispatch('getStatusAdmin', res.data.username);
                        //this.$router.push('/home-adm')


                        let userCompleto = res.data.user
                        let userCompletoSTR = JSON.stringify(userCompleto)
                        sessionStorage.setItem('admin', userCompletoSTR)
                        this.$store.dispatch('getStatusAdmin', userCompletoSTR);
                        this.$router.push('/home-adm')
                    }
                })
                .catch(error => {
                    console.log(error)
                    this.error = true
                    this.loading = false
                    setTimeout(() => {
                        this.error = false
                    }, 5000)
                })
        },
        send() {
            this.$v.$touch()
            if (!this.$v.$invalid) {
                let form = {
                    email: this.$v.f.email.$model,
                    password: this.$v.f.password.$model,
                }
                this.loading = true
                this.sendDataFormAxios(form)
            }
        },
        resetForm() {
            return {
                email: '',
                password: ''
            }
        }
        }, 
    computed: {

    }
}


</script>

<style scoped lang="css">
  .src-components-login {

  }
</style>
