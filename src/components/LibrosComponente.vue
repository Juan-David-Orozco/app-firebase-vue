<template>
  <div class='row my-1 p-1 bg-light text-primary'>
    <div class='col-5 my-auto'>{{libro.nombre}}</div>
    <div class='col-5 my-auto'>{{libro.autor}}</div>
    <div class='col-2'>
      <div
        id='eliminar' class='btn btn-danger btn-sm'
        v-on:click='manejarClick($event,{indice:indice, id:id})'
        style="width:100%"
      ><i class="fa fa-trash" aria-hidden="true"></i></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'librosComponente',
  props: ['libro','id','indice'],
  data: function () {
    return {
      test: 'test'
    }
  },
  methods: {
    manejarClick: function (evento,libroID) {
      //console.log('ID: '+indice)
      if (evento.target.id==='eliminar'){
        this.$emit('eliminarLibro',libroID)
      }
    }
  }
}
</script>

<style>

</style>
