<template>
    <div id="nav">
      <NavBar/> 
    </div>

    <div v-if="this.role == 3"> 
    <section class="SecretariatPage">
        <div class="text-center">
                <h1 class="form-title">Secrétariat</h1>
        </div>
        <div class="SecretariatContainer">
        <div class="text-center">
            <div class="row">
                <div class="col">
                <div class="nav-links">
                    <button class="btn btn-success btn-lg">
                        <router-link class="nav-link" id="eleve" to="/secretariat/eleves">
                            <i class="fas fa-solid fa-graduation-cap"></i>  Gestion des élèves
                        </router-link>
                    </button>
                </div>
                </div>
                <div class="col">
                <div class="nav-links">
                    <button class="btn btn-secondary btn-lg">
                        <router-link class="nav-link" id="classe" to="/secretariat/classes">
                            <i class="fas fa-solid fa-school"></i>  Gestion des classes
                        </router-link>
                    </button>
                </div>
                </div>
                <div class="col">
                <div class="nav-links">
                    <button class="btn btn-primary btn-lg">
                        <router-link class="nav-link" id="actualite" to="/secretariat/actualites">
                            <i class="fas fa-regular fa-calendar-check"></i>  Gestion des actualités
                        </router-link>
                    </button>
                </div>
                </div>
            </div>
        </div>
        </div>
    </section>
    </div>

    <div v-else></div>
</template>

<script>
import NavBar from './NavBar.vue'
import axios from 'axios'

const url = require("../../url/url.js")

    export default{
        name: 'SecretariatPage',

        components: {
            NavBar
        },

        data(){
            return{
                role: ""
            }
        },

        created(){
            this.getRole(localStorage.getItem('mail'));
        },

        methods: {
            async getRole(mail){
                let destinationUrl = url.concatUrl(`/role/${mail}`)

                await axios.get(destinationUrl)
                .then(response =>{
                    this.role = response.data[0].Roles;
                })
                .catch(error =>{
                    console.log(error);
                })
                this.checkRole();

            },

            checkRole(){
                if(this.role == 3){
                    return true;
                }
                else{
                    this.$router.push("/");
                    return false;
                }
            }
        }
    }


</script>


<style>
.btn-primary {
    background-color: #6dabe4;
}

.btn-success{
    background-color: #71c770;
}
.btn-danger{
    background-color: #df5757;
}
.nav-link {
  font-family:'Poppins';
  color: white;
  padding: 0;
}



.SecretariatPage{
    /*
    background-image: url("../../public/images/background-school-2.jpg");*/
    background-size: contain;
    padding-top: 10%;
    padding-bottom: 10%;
}
.SecretariatContainer{
    padding-left: 20%;
    padding-right: 20%;
    margin: 0 auto;
    margin-top: 125px;
}



</style>