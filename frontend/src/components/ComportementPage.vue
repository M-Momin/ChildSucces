<template>
    <div id="nav">
      <NavBar/> 
    </div>

    <form @submit.prevent="" >
        <div class="container-comportement">
            <div data-aos="zoom-in-left" data-aos-duration="1250">
                <div class="wrapper">
                    <header>Comportement 
                    </header>

                    <div class="row titles">
                            <div class="col-sm title">
                                Enfant
                            </div>
                            <div class="col-sm title">
                                Mail du professeur
                            </div>
                            <div class="col-sm title">
                                Commentaire
                            </div>
                            <div class="col-sm title">
                                Date
                            </div>
                            <div class="col-sm title">
                                Signature
                            </div>
                    </div>
                    <CompComponent v-bind:mail="item.Mail" v-bind:date="this.conversionDate(item.Date)" v-bind:eleveId="item.EleveID" v-bind:contenu="item.Contenu" v-bind:signature="item.Signature"  v-for="item in items" :key="item"/>
                    <button class="button-send" v-on:click="updateSignature()"  name="button">Signer tout</button>
                </div>
            </div>
        </div>
        
    </form>
</template>
<script>
import NavBar from './NavBar.vue'
import CompComponent from "./CompComponent.vue";
import axios from 'axios';
const url = require("../../url/url.js");




export default{
    name : "ComportementPage",

    components: {
        CompComponent,
        NavBar
    },



    data(){
        return {
            items: [],
            eleve_id: [],
        }
    },


    created(){
        this.getEleveIdByMailParent();
    },

    methods : {
        async getEleveIdByMailParent(){
            let destinationUrl = url.concatUrl('/eleveid/' + localStorage.getItem('mail'));
            await axios.get(destinationUrl)
            .then(response =>{
                this.eleve_id = (response.data[0]);
                this.test(this.eleve_id.length);
            })
            .catch(error =>{
                console.log(error)
            })
        },
        test(tab){
            for(let i=0; i<tab; i++){
                let finalId = this.eleve_id[i].EleveID;
                this.getComportement(finalId);
            }
        },
        async getComportement(id){
            try{
                let destinationUrl = url.concatUrl('/comportement/' + id);
                await axios.get(destinationUrl)
                .then(response =>{
                    this.items.push(response.data[0]);
                    //filter le tableau en fonction des événements récents et nombres de commentaires
                    this.items.reverse().slice(0,20);
                })
                .catch(error =>{
                    console.log(error)
                })
            }
            catch(error){
                console.log(error);
            }

        },
         async updateSignature(){
            let destinationUrl = url.concatUrl('/updateSignature' + this.eleve_id);
            await axios.post(destinationUrl)
            .then(function(){
                location.reload();
            })
            .catch(error =>{
                console.log(error)
            })
        },

        conversionDate(date){
            let result = new Date(date);

            return result.toLocaleDateString();
        },
    
    }


}


</script>
<style scoped>

*{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Poppins'
}
.container-comportement {
    margin-top: 150px;
    padding-bottom: 10%;
}
.wrapper {
    margin-left: auto;
    margin-right: auto;
    background: #fff;
    border-radius: 20px;
    border: 3px solid #6dabe4;
    box-shadow: 10px 10px 10px rgba(0,0,0,0.05);
    min-width: 600px;
}
.wrapper header {
    font-size: 36px;
    text-align: center;
    font-weight: 300;
    padding: 20px 30px;
    border-bottom: 3px solid #6dabe4;
}

.title {
    margin-top: 0px;
    font-weight: bold;
}

.titles {
    margin-left: 15px;
    margin-top: 15px;
}

form .button-send {
    margin: 50px 0;
    text-align: center;
}

.button-send {
    color : #fff;
    border: none;
    outline: none;
    font-size: 15px;
    cursor: pointer;
    border-radius: 5px;
    padding: 13px 25px;
    background: #6dabe4;
    transition: background 0.3s ease;
    transition: transform 400ms;
}

.button-send:hover {
    transform: scale(1.10);
}

 form .button-send {
        margin-top: 20px;
        float:right;
        flex-direction: column;
    }

    .button-send button {
        width: 100%;
        padding: 11px;
        font-size: 16px;
    }

@media (max-width: 700px) {
    .wrapper header {
        text-align: center;
        font-weight: 300;
        padding: 20px 30px;
        border-bottom: 3px solid #6dabe4;
    }
}
</style>