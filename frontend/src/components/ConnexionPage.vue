<template>
    <div id="nav">
      <NavBar/> 
    </div>

    <section class="connexion">
            <div data-aos="zoom-in-left" data-aos-duration="1250">
        <div class="container">
            <div class="connexion-group">
                <div class="connexion-form">
                    <h2 class="form-title">Connexion</h2>

                    <form action="" class="formulaire" id="formulaire" @submit.prevent ="SendData()">
                        <div class="inputs">
                            <label for="email" class="input-label"></label>
                            <input type="email" name="email" id="email" placeholder="Votre Email" autocomplete="off" v-model="email" class="inscription-input">
                        </div>
                        <div class="inputs">
                            <label for="password" class="input-label"></label>
                            <input type="password" name="password" id="password" placeholder="Mot de passe" autocomplete="off" v-model="password" class="inscription-input">
                        </div>

                        <div class="input-button">
                            <button type="submit" class="submit-bouton" name="connexion" id="btn-connexion" :disabled="validatedFields" >Se connecter</button>
                        </div>
                    </form>
                </div>

                <div class="alertMessage">
                    <p>{{message}}</p>
                </div>

                <div class="link">
                    <router-link to="inscription" class="inscription-link">Créer un compte</router-link>
                </div>
            </div>
        </div>
        </div>
    </section>
</template>

<script>
import {mapState} from 'vuex';
import axios from 'axios';
import NavBar from './NavBar.vue'

const url = require("../../url/url.js");

    export default{
        name : "ConnexionPage",

        data(){
            return {
                email: '',
                password: '',

                mail_exist: '',
                password_exist: '',

                message: "",
            }
        },

        components:{
            NavBar,
        },

        computed:{
            validatedFields : function(){
                if(this.email != "" && this.password != ""){
                    return false;
                }
                else{
                    return true;
                }
            },
            ...mapState(['status'])
        },

        methods: {

            checkMailDatabase(mail){
                const destinationUrl = url.concatUrl(`/users/${mail}`);

                return new Promise((resolve, reject) =>{
                    axios.get(destinationUrl)
                    .then(response =>{
                        this.mail_exist = response.data.length;
                        resolve(response);
                    })
                    .catch(error =>{
                        reject(error);
                    })
                })
            },

            getPasswordByMail(mail){
                const destinationUrl = url.concatUrl(`/passwords/${mail}`);

                return new Promise((resolve,reject) =>{
                    axios.get(destinationUrl)
                    .then(response =>{
                        this.password_exist = response.data[0].MotDePasse;
                        resolve(response);
                    })
                    .catch(error =>{
                        reject(error);
                    })
                })
            },

            async SendData(){
                await this.checkMailDatabase(this.email);
                if(this.mail_exist == "1"){
                    await this.getPasswordByMail(this.email);
                    if (this.password == this.password_exist){
                        this.message = "";
                        this.login();
                    }
                    else{
                        this.message = "email ou mot de passe invalide réessayez"
                    }
                }
                else{
                    this.message = "Veuillez remplir des informations valides"
                }
            },


            async login(){
                 
                const self = this;

                await this.$store.dispatch('login',this.email)
                .then(function(){
                    self.$router.push('/profile');
                })
                .catch(error =>{
                console.log(error);
                })
            },

        }
    }
</script>


<style>
@font-face {
    font-family: 'magical_holidayregular';
    src: url('../../public/fonts/MagicalHollyday/magical_holiday-webfont.woff2') format('woff2'),
         url('../../public/fonts/MagicalHollyday/magical_holiday-webfont.woff') format('woff');
    font-weight: normal;
    font-style: normal;
}

@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300&display=swap');

@media screen and (max-width: 1200px) {
    .container {
        width: calc( 100% - 30px);
        max-width: 100%; } }
@media screen and (min-width: 1024px) {
.container {

    max-width: 1200px; } }
@media screen and (max-width: 768px) {
.signup-content, .signin-content {
    flex-direction: column;
    -moz-flex-direction: column;
    -webkit-flex-direction: column;
    -o-flex-direction: column;
    -ms-flex-direction: column;
    justify-content: center;
    -moz-justify-content: center;
    -webkit-justify-content: center;
    -o-justify-content: center;
    -ms-justify-content: center; }
}

@media screen and (max-width: 400px) {
    .social-login {
    flex-direction: column;
    -moz-flex-direction: column;
    -webkit-flex-direction: column;
    -o-flex-direction: column;
    -ms-flex-direction: column; }

    .social-label {
    margin-right: 0px;
    margin-bottom: 10px; } }

.alertMessage{
    color: red;
    margin-left: 32%;
}
 
h2 {
    line-height: 1.66;
    margin: 0;
    padding: 0;
    font-weight: bold;
    color: #222;
    font-size: 36px; }

.inscription-input {
    width: 100%;
    display: block;
    border: none;
    border-bottom: 1px solid #999;
    padding: 6px 30px;
    box-sizing: border-box; }
    .inscription-input::-webkit-input-placeholder {
        color: #999; }
    .inscription-input::-moz-placeholder {
        color: #999; }
    .inscription-inputt:-ms-input-placeholder {
        color: #999; }
    .inscription-input:-moz-placeholder {
        color: #999; }
    .inscription-input:focus::-webkit-input-placeholder {
    color: #222; }
    .inscription-input:focus::-moz-placeholder {
    color: #222; }
    .inscription-input:focus:-ms-input-placeholder {
    color: #222; }
    .inscription-input:focus:-moz-placeholder {
    color: #222; }

    .inscription-input:focus{
        outline-style:none;
        box-shadow:none;
        border-color:black;
        background-color: white;
    }

    .inscription-input:active{
        background-color: white;
    }   

label {
    position: absolute;
    left: 0;
    top: 50%;
    transform: translateY(-50%);
    -moz-transform: translateY(-50%);
    -webkit-transform: translateY(-50%);
    -o-transform: translateY(-50%);
    -ms-transform: translateY(-50%);
    color: #222; 
}


.container {
    border: 3px solid #6dabe4;
    width: 900px;
    background: #fff;
    margin: 0 auto;
    box-shadow: 0px 15px 16.83px 0.17px rgba(0, 0, 0, 0.05);
    -moz-box-shadow: 0px 15px 16.83px 0.17px rgba(0, 0, 0, 0.05);
    -webkit-box-shadow: 0px 15px 16.83px 0.17px rgba(0, 0, 0, 0.05);
    -o-box-shadow: 0px 15px 16.83px 0.17px rgba(0, 0, 0, 0.05);
    -ms-box-shadow: 0px 15px 16.83px 0.17px rgba(0, 0, 0, 0.05);
    border-radius: 20px;
    -moz-border-radius: 20px;
    -webkit-border-radius: 20px;
    -o-border-radius: 20px;
    -ms-border-radius: 20px; 
}

.form-title {
    font-family:'Poppins';
    font-weight: 200;
    margin-bottom: 33px;
}

.connexion {
    margin-top: 3.5%;
    padding-top: 100px;
    padding-bottom: 10%;
}

.connexion-group {
    padding: 75px 0;
}

.connexion-form {
    margin: 0 auto;
    width: 50%;
    overflow: hidden;
    padding-bottom: 15px;
}

.submit-bouton {
    display: inline-block;
    background: #6dabe4;
    color: #fff;
    border: none;
    font-family: 'Poppins';
    width: auto;
    padding: 15px 39px;
    border-radius: 5px;
    -moz-border-radius: 5px;
    -webkit-border-radius: 5px;
    -o-border-radius: 5px;
    -ms-border-radius: 5px;
    margin-top: 25px;
    cursor: pointer;
    transition: transform 400ms;
}

.submit-bouton:hover {
    transform: scale(1.20);
}

.input-button {
    margin-top: 10px;
    text-align: center;
}

.inputs {
    position: relative;
    margin-top: 50px;
    overflow: hidden; }

.inputs:last-child {
    margin-bottom: 0px; 
}

.connexion-image {
    max-width: 40px;
    max-height: auto;
}

.inscription-link {
    font-size: 14px;
    color: #222;
    display: block;
    text-align: center;
    text-decoration: none;
    font-family: 'Poppins';
    font-size: 15px;
    transition: transform 400ms;
 }

.inscription-link:hover {
    transform: scale(1.25);
}

.link {
    margin-top: 30px;
}
</style>