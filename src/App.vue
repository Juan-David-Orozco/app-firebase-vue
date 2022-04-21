<template>
  <div id="app">
    <div id='login' v-if='!logon' >
      <loginForm
        v-bind:firebase="firebase"
        v-on:ingresoCorrecto='ingresoCorrecto($event)'>
      </loginForm>
    </div>
    <div id='inicio' class="m-2 p-2" v-else>
      <div id='librosContainer' class='container border p-2'>
        <div class='bg-dark text-white text-center my-1'>
          <h1>Lista de libros a leer</h1>
        </div>
        <div class="container text-center mt-4">
          <div class='row lead border rounded bg-light text-primary p-2 mt-3'>
          <div class='col-sm-3 col-md-10 mx-auto my-auto'>
            Nombre del libro
          </div>
          <div class='col-sm-3 col-md-8 mx-auto'>
            <input v-model='nombreLibro' class='text-primary' type='text' style="width:100%">
          </div>
          <div class='col-sm-3 col-md-10 mx-auto my-auto'>
            Autor del libro
          </div>
          <div class='col-sm-3 col-md-8 mx-auto'>
            <input v-model='autorLibro' class='text-primary' type='text' style="width:100%">
          </div>
          <div class='col-12 my-2'>
            <div
              id='agregar' class='btn btn-primary'
              v-on:click='manejarClick($event)'
            >Agregar Libro</div>
          </div>
        </div>
        </div>
        <div class='container text-center mt-4'>
          <div class='row lead border rounded bg-dark text-white py-1'>
            <div class='col-5'>
              Nombre del Libro
            </div>
            <div class='col-5'>
              Autor del Libro
            </div>
            <div class='col-2'>
              Accion
            </div>
          </div>
          <librosComponente
            v-for="(libro,index) in libros"
            v-bind:libro='libro'
            v-bind:id='libro.id'
            v-bind:indice='index'
            v-bind:key='index'
            v-on:eliminarLibro='eliminar($event)'
          ></librosComponente>
        </div>
      </div>
      <div
        class="col-4 btn btn-danger mx-auto my-2"
        @click="salir()"
      >
        Salir
      </div>
    </div>
  </div>
</template>

<script>

import firebase from 'firebase'
import 'firebase/firestore'
import librosComponente from './components/LibrosComponente.vue'
import loginForm from './components/Form.vue';

export default {
  name: 'app',
  data: function (){
    return {
      libros :[],
      nombreLibro:'',
      autorLibro:'',
      coleccion:{},
      idUsuario:'',
      logon:false,
      firebase:'',
      db:'',
    }
  },
  components: {
    loginForm,
    librosComponente
  },
  beforeMount: function () {
    const firebaseConfig = {
      apiKey: "AIzaSyBR1umMRC-2dLGd2FQJ29rXzDtboO52sck",
      authDomain: "fir-firestore-b058c.firebaseapp.com",
      projectId: "fir-firestore-b058c",
      storageBucket: "fir-firestore-b058c.appspot.com",
      messagingSenderId: "66465685071",
      appId: "1:66465685071:web:40a878f476d85eeb77543a"
    };
    firebase.initializeApp(firebaseConfig);
    this.db = firebase.firestore();
    const settings = {timestampsInSnapshots: true};
    this.db.settings(settings);
    this.firebase = firebase
    console.log(this.db)
    console.log(this.firebase)
  },
  methods: {
    manejarClick: function (evento) {
      if (evento.target.id==='agregar'){
        const libroData = {
          nombre:this.nombreLibro,
          autor:this.autorLibro
        }
        this.coleccion.add(libroData)
        .then((docReference) => {
          this.libros.unshift({
            id:docReference.id,
            nombre: libroData.nombre,
            autor: libroData.autor
          })
        })
        .catch((error) => {
          alert('No se pudo agregar el libro al sistema. Error: '+error.message)
        })
        this.nombreLibro=''
        this.autorLibro=''
      }
    },
    eliminar: function (libroID){
      this.coleccion.doc(libroID.id).delete()
      this.libros.splice(libroID.indice,1)
    },
    ingresoCorrecto: function(usuario) {
      console.log('User: '+ usuario)
      this.idUsuario = usuario
      this.logon = true
      this.coleccion = this.db.collection('/usuarios/'+usuario+'/libros')
      console.log(this.coleccion)
      this.coleccion.get()
      .then((libros)=>{
        libros.forEach((libro) => {
          this.libros.push({
            id:libro.id,
            nombre:libro.data().nombre,
            autor:libro.data().autor
          })
        })
      })
    },
    salir: function(){
      this.logon = false
      this.libros = []
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
