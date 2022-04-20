<template>
  <div id="app">
    <div id='login' v-if='!logon' >
      <loginForm
        v-bind:firebase="firebase"
        v-on:ingresoCorrecto='ingresoCorrecto($event)'>
      </loginForm>
    </div>
    <div id='inicio' v-else>
      <div>Usuario: {{userData.usuario}} </div>
      <div>Tipo de gasto: {{userData.gastos.tipo}} </div>
      <div>Monto del Gasto: {{userData.gastos.monto}} </div>
      <div
        class="col-4 btn btn-info mx-auto my-1"
        @click="logon=false"
      >
        Salir
      </div>
    </div>
  </div>
</template>

<script>

import firebase from 'firebase'
import 'firebase/firestore'
import loginForm from './components/Form.vue';

export default {
  name: 'app',
  data: function (){
    return {
      coleccionRaiz:'usuarios',
      idUsuario:'',
      coleccionGastos:'gastos',
      docAlquilerCasa: 'vehiculos',
      userData:{},
      logon:false,
      firebase:'',
      db:'',
      monto: 0
    }
  },
  components: {
    loginForm
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
    ingresoCorrecto: function(usuario) {
      console.log('User: '+ usuario)
      this.idUsuario = usuario
      this.logon = true
      const doc = this.db.collection(this.coleccionRaiz).doc(this.idUsuario)
      console.log(doc)
      this.userData = {
        usuario: doc.id,
        gastos: {}
      }
      console.log(this.userData)
      const gastos = doc.collection(this.coleccionGastos)
      //const alquilerCasa = gastos.doc('alquiler_casa')
      const alquilerCasa = gastos.doc(this.docAlquilerCasa)
      const montoTemp = this.monto
      this.userData.gastos = {
        tipo: alquilerCasa.id,
        monto: montoTemp
      }
      alquilerCasa.get().then((doc)=>{
        this.userData.gastos.monto = doc.data().monto
      })
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
