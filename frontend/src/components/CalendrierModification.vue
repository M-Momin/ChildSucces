<template>
    <div id="nav">
      <NavBar/> 
    </div>

<div v-if="this.role == 2">
<div class="calendrier-modification">
<div class="row">
    <div class="col-sm">
        <form class='event' @submit.prevent="sendData()">
        <h3 class="title"> Ajout d'une activité <i class="fas fa-solid fa-calendar-plus"></i></h3>
        <div class="row">
            <div class="col-5">
            <h5>Titre:</h5>
                <input type="text" class="form-control class-select" id="exampleInputEmail1" v-model="titre" placeholder="Choisissez un titre...">
            </div>
            <div class="col-5">
            <h5>Classe:</h5>
                <select class="defautSelect" id="object1" v-model="classe">
                    <option value="" selected>Choisissez une classe</option>
                    <option v-for="item in items[0]" :key="item.Classe" v-bind:value="item.Classe">{{item.Classe}}</option>
                </select>
            
            </div>
            </div>
        <div class="row">
            <div class="col-5">
                <h5>Jour et heure de départ:</h5>
                <input type="datetime-local" id="départ" v-model="start">
            </div>
            <div class="col-5">
                <h5>Jour et heure de fin:</h5>
                <input type="datetime-local" id="fin" v-model="stop">
            </div>

        </div>

        <div class='row'>
            <div class="col-10">
                <h5>Contenu:</h5>
                <textarea class="form-control" placeholder="Contenu de l'actualité..." aria-label="With textarea" v-model="contenu"></textarea>
            </div>
        </div>
            

        <button type="submit" class="btn btn-success"  :disabled="validatedFields" >Ajouter <i class="fas fa-solid fa-plus"></i></button>
        </form>
        </div>



        <div class="col-sm">
        <form class='event'>
        <h3 class="title"> Suppression d'une activité <i class="fas fa-solid fa-trash"></i></h3>
        <div class="row">
            
            <div class="col-5">
            <h5>Classe:</h5>
                <select id="object2" class="defautSelect" v-model="classeDelete">
                    <option value="" selected>Choisissez une classe</option>
                    <option v-for="item in items[0]" :key="item.Classe" v-bind:value="item.Classe">{{item.Classe}}</option>
                </select>
            </div>
        
            <div class="col-5">
                <h5>Date:</h5>
                <input type="date" class="form-control" id="exampleInputPassword1" v-model="dateDelete">
            </div>
            </div>
            <p class="advice"> *Chercher par classe et date pour trouver l'activité à supprimer </p>
            <div class="row">
                <div class="col-sm">
                    <button type="button" v-on:click="getEventByClassAndDate()" :disabled="validInputs" class="btn btn-success" >Rechercher</button>
                </div>
            </div>
            <div class="row">
            <div class="col-10">
                <h5>Activités trouvées:</h5>
            </div>
            <div class="row">
                <div class="col-4">
                    <h6> Titre: </h6>
                </div>
                <div class="col-3">
                    <h6> Début: </h6>
                </div>
                <div class="col-3">
                    <h6> Fin: </h6>
                </div>
            </div>
            <div class="table-container">
            <div>
                <GestionCalendrier v-for="event in this.events[0]" :key="event" v-bind:eventData="event"></GestionCalendrier>

            </div>
            </div>
        </div>
        </form>
        </div>
    

        </div>
    </div>
</div>

<div v-else></div>
</template>

<script>
const url = require("../../url/url.js");
import NavBar from './NavBar.vue'
import GestionCalendrier from "./GestionCalendrier.vue"
import axios from 'axios';
export default{
    name: 'CalendrierModification',
    classe: String,

    data(){
        return{
            items : [],
            events: [],
            contenu: '',
            start:'',
            stop:'',
            classe:'',
            titre:'',
            revele: false,
            classeDelete: '',
            dateDelete:'',
            titreDelete:'',
            role: "",
        }
    },
    components: {
        GestionCalendrier,
        NavBar
            
    },
    created(){
        this.getClasses();
        this.getRole(localStorage.getItem('mail'))
    },

    computed: {
        validatedFields : function(){
            if(this.contenu != "" && this.titre!= ""  && this.classe != ""){
                return false;
            }
            else{
                return true;
            }
        },

        validInputs : function(){
            if(this.dateDelete != ""   && this.classeDelete != ""){
                return false;
            }
            else{
                return true;
            }
        }
    },

    methods:{
        async getClasses(){

            let destinationUrl = url.concatUrl("/classes");
            await axios.get(destinationUrl)
            .then(response =>{
                this.items = response.data;
            })
            .catch(function(){
                //pass

            })
        },

        toggleModale: function(){
            this.revele=!this.revele;
        },

        async sendData(){

            let destinationUrl = url.concatUrl('/calendrier');
            await axios.post(destinationUrl,{
                Classe : this.classe,
                Date: this.start,
                Contenu: this.contenu,
                Titre: this.titre,
                StartTime: this.start,
                StopTime: this.stop,
            })
            .then(function(){
            })
            .catch(error =>{
                console.log(error);
            })

            this.$router.push('/calendrier');
        },
        async getEventByClassAndDate(){
            
            let destinationUrl = url.concatUrl('/calendrier' + '/' + this.classeDelete + '/' + this.dateDelete) ;
            await axios.get(destinationUrl)
            .then(response =>{
                this.events = response.data;
                //console.log((this.events[0][0]).Titre);
            })
            .catch(function(){
                //pass
            })
        },

        async getRole(mail){
            let destinationUrl = url.concatUrl(`/role/${mail}`)

            await axios.get(destinationUrl)
            .then(response =>{
                this.role = response.data[0].Roles;
            })
            .catch(error =>{
                console.log(error);
            })

        },   

    }

}
</script>

<style scoped>
    .table-container {
        display:flex;
        flex-direction:column;
        overflow-x:scroll;
        overflow-y:scroll;
        height:200px;
    }
.event{
    height: 250px;
    margin-top: 150px;
    margin-bottom: 100px;
    margin-left: 10%;
    min-width: 500px;
}
.title{
    margin-bottom: 40px;
}

.date-input{
    max-width: 394px;
}

.row{
    padding-bottom: 20px;
    min-width: 250px;
}
.advice{
    padding-bottom: 20px;
    color: #6dabe4;
    font-style: italic;
    margin-top: 10px;
}
.defautSelect{
    height: 37.5px;
}

</style>
    
