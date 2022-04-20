<template>
  <div class="container border rounded" style="width: 50%" v-if="registro">
    <div id="titulo" class="row my-2">
      <h1>Registro del Sistema</h1>
    </div>
    <div id="form" class="row mt-0">
      <div class="col-12">

        <div class="form-group my-1">
          <label for="name" class="cols-sm-2 control-label">{{
            campo_nombre
          }}</label>
          <div class="cols-sm-10">
            <div class="input-group">
              <span class="pt-2 px-3 input-group-addon bg-info"
                ><i class="fa fa-user fa" aria-hidden="true"></i
              ></span>
              <input
                type="text"
                class="form-control"
                name="name"
                id="name"
                placeholder="Nombre Completo"
              />
            </div>
          </div>
        </div>

        <div class="form-group my-1">
          <label for="email" class="cols-sm-2 control-label">{{
            campo_email
          }}</label>
          <div class="cols-sm-10">
            <div class="input-group">
              <span class="pt-2 px-3 input-group-addon bg-info"
                ><i class="fa fa-envelope fa" aria-hidden="true"></i
              ></span>
              <input
                type="text"
                class="form-control"
                name="email"
                id="email"
                placeholder="Correo Electrónico"
                v-model="correo"
              />
            </div>
          </div>
        </div>

        <div class="form-group my-1">
          <label for="password" class="cols-sm-2 control-label">{{
            campo_pass
          }}</label>
          <div class="cols-sm-10">
            <div class="input-group">
              <span class="pt-2 px-3 input-group-addon bg-info"
                ><i class="fa fa-lock fa-lg" aria-hidden="true"></i
              ></span>
              <input
                type="password"
                class="form-control"
                name="password"
                id="password"
                placeholder="Ingrese la contraseña"
                v-model="clave"
              />
            </div>
          </div>
        </div>

        <div class="form-group my-1">
          <label for="confirm" class="cols-sm-2 control-label">{{
            campo_pass_confirm
          }}</label>
          <div class="cols-sm-10">
            <div class="input-group">
              <span class="pt-2 px-3 input-group-addon bg-info"
                ><i class="fa fa-lock fa-lg" aria-hidden="true"></i
              ></span>
              <input
                type="password"
                class="form-control"
                name="confirm"
                id="confirm"
                placeholder="Confirme la contraseña"
                v-model="confirmClave"
              />
            </div>
          </div>
        </div>

        <div class="form-group row my-2">
          <div
            class="col-sm-5 btn btn-primary mx-auto my-1"
            @click="manejoClick($event)"
          >
            Resgistrar
          </div>
          <div
            class="col-sm-5 btn btn-info mx-auto my-1"
            @click="registro=false"
          >
            Cancelar
          </div>
        </div>

      </div>
    </div>
  </div>
  <div v-else>
    <div id="login">
      <div class="container my-3 border rounded" style="width: 50%">
        <div class="row">
          <div class="col-12 text-center my-2"><h3>Ingreso al Sistema</h3></div>
        </div>
        <div class="form-group my-1">
          <label for="email" class="cols-sm-2 control-label"
            >Correo Electrónico</label
          >
          <div class="cols-sm-10">
            <div class="input-group">
              <span class="pt-2 px-3 input-group-addon bg-info">
                <i class="fa fa-envelope fa" aria-hidden="true"></i>
              </span>
              <input
                v-model="correo"
                type="text"
                class="form-control"
                placeholder="Ingrese el Correo Electrónico"
              />
            </div>
          </div>
        </div>
        <div class="form-group my-1">
          <label for="password" class="cols-sm-2 control-label"
            >Contraseña</label
          >
          <div class="cols-sm-10">
            <div class="input-group">
              <span class="pt-2 px-3 input-group-addon bg-info">
                <i class="fa fa-lock fa-lg" aria-hidden="true"></i>
              </span>
              <input
                type="password"
                class="form-control"
                placeholder="Ingrese la contraseña"
                v-model="clave"
              />
            </div>
          </div>
        </div>
        <div class="form-group my-2">
          <button type="button" class="btn btn-primary" @click="ingresar">
            Ingresar
          </button>
        </div>
        <div class="usuario_nuevo text-center">
          <a href="#" @click="registro=true">¿Usuario nuevo?</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "loginForm",
  data: function () {
    return {
      campo_nombre: "Nombre Completo",
      campo_email: "Correo Electrónico",
      campo_pass: "Contraseña",
      campo_pass_confirm: "Confirme la contraseña",
      correo: "",
      clave: "",
      confirmClave: "",
      registro: false,
    };
  },
  props: ["firebase"],
  methods: {
    manejoClick: function () {
      var email = "";
      this.firebase
        .auth()
        .createUserWithEmailAndPassword(this.correo, this.clave)
        .then((response) => {
          console.log(
            "Registro Correcto " +
              response.user.email +
              " Registro: " +
              this.registro
          );
          this.correo = response.user.email;
          this.registro = false;
        })
        .catch(function (error) {
          var errorCode = error.code;
          var errorMessage = error.message;
          console.log("Error: " + errorCode + " - " + errorMessage);
        });
      console.log("Email: " + email);
    },
    ingresar: function () {
      this.firebase
        .auth()
        .signInWithEmailAndPassword(this.correo, this.clave)
        .then((response) => {
          console.log("Ingreso correcto con User: " + response.user.email);
          this.$emit("ingresoCorrecto", response.user.email);
        })
        .catch((error) => {
          //var errorCode = error.code;
          //var errorMessage = error.message;
          console.log("Error: " + error.code + " - " + error.message);
        });
    },
  },
};
</script>

<style>
#form {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
}
.usuario_nuevo {
  text-align: right;
}
</style>
