<template>
  <div class="register">
    <div class="wrapper fadeInDown">
      <div id="formContent">
        <!-- Icon -->
        <div class="fadeIn first">
          <img src="@/assets/logo.png" id="icon" alt="User Icon" />
        </div>

        <form v-on:submit.prevent="register">

          <div>
            <input type="radio" id="ciRadio" value="CI" v-model="tipoIdentificacion" name="tipoIdentificacion">
            <label for="ciRadio">CI</label>
          </div>
          <div>
            <input type="radio" id="rucRadio" value="RUC" v-model="tipoIdentificacion" name="tipoIdentificacion">
            <label for="rucRadio">RUC</label>
          </div>
          <div>
            <input type="radio" id="pasaporteRadio" value="Pasaporte" v-model="tipoIdentificacion"
              name="tipoIdentificacion">
            <label for="pasaporteRadio">Pasaporte</label>
          </div>

          <input v-if="tipoIdentificacion === 'CI'" type="text" class="fadeIn sixth" placeholder="CI"
            v-model="identificacion" minlength="10" maxlength="10" required @input="search">
          <input v-if="tipoIdentificacion === 'RUC'" type="text" class="fadeIn sixth" placeholder="RUC"
            v-model="identificacion" minlength="13" maxlength="13" required @input="search">
          <input v-if="tipoIdentificacion === 'Pasaporte'" type="text" class="fadeIn sixth" placeholder="Pasaporte"
            v-model="identificacion" minlength="6" maxlength="20" required @input="search">

          <input type="text" class="fadeIn second" placeholder="Usuario" v-model="usuario" maxlength="12" required>
          <input type="password" class="fadeIn third" placeholder="Contraseña" v-model="contrasenia" maxlength="12"
            required>
          <input type="password" class="fadeIn fourth" placeholder="Confirmar Contraseña" v-model="confirmarContrasenia"
            maxlength="12" required>
          <input type="text" class="fadeIn fourth" placeholder="Nombre" v-model="nombre"
            @input="validarSoloLetras('nombre')" maxlength="15" required>
          <input type="text" class="fadeIn fifth" placeholder="Apellido" v-model="apellido"
            @input="validarSoloLetras('apellido')" maxlength="15" required>
          <input type="submit" class="fadeIn fourth" value="Registrar">

          <input type="button" class="fadeIn fourth" value="Eliminar" @click="eliminarUsuario">

          <input type="button" class="fadeIn fourth" value="Modificar" @click="modificarUsuario">

        </form>

      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'RegisterView',
  components: {

  },
  data() {
    return {
      usuario: '',
      contrasenia: '',
      confirmarContrasenia: '',
      nombre: '',
      apellido: '',
      identificacion: '',
      tipoIdentificacion: ''
    };
  },
  methods: {
    register() {
      if (this.contrasenia !== this.confirmarContrasenia) {
        alert('Las contraseñas no coinciden');
        return;
      }

      const usuario = {
        usuario: this.usuario,
        contrasenia: this.contrasenia,
        confirmarContrasenia: this.confirmarContrasenia,
        nombre: this.nombre,
        apellido: this.apellido,
        identificacion: this.identificacion
      };

      axios.post('http://localhost:8090/api/v1/registro', usuario)
        .then(() => {
          alert('Usuario registrado correctamente');
        });
    },
    validarSoloLetras(campo) {
      // Obtener el valor del campo
      let valorCampo = this[campo];

      // Reemplazar todos los caracteres no alfabéticos con una cadena vacía
      this[campo] = valorCampo.replace(/[^A-Za-z]/g, '');
    },
    search() {
      if (this.identificacion !== '') {
        axios.get(`http://localhost:8090/api/v1/registro/${this.identificacion}`)
          .then(response => {
            // Asignar los datos obtenidos a los campos correspondientes
            this.usuario = response.data.usuario.usuario;
            this.nombre = response.data.usuario.nombre;
            this.contrasenia = response.data.usuario.contrasenia;
            this.confirmarContrasenia = response.data.usuario.confirmarContrasenia;
            this.apellido = response.data.usuario.apellido;
          })
          .catch(error => {
            console.error('Error al buscar:', error);
          });
      }
    },
    eliminarUsuario() {
      if (this.identificacion !== '') {
        axios.delete(`http://localhost:8090/api/v1/registro/${this.identificacion}`)
          .then(response => {
            alert(response.data.mensaje);
            // Limpiar los campos después de eliminar el usuario
            this.usuario = '';
            this.contrasenia = '';
            this.confirmarContrasenia = '';
            this.nombre = '';
            this.apellido = '';
            this.identificacion = '';
          })
          .catch(error => {
            console.error('Error al eliminar usuario:', error);
          });
      }
    },
    modificarUsuario() {
      if (this.contrasenia !== this.confirmarContrasenia) {
        alert('Las contraseñas no coinciden');
        return;
      }

      const usuario = {
        usuario: this.usuario,
        contrasenia: this.contrasenia,
        confirmarContrasenia: this.confirmarContrasenia,
        nombre: this.nombre,
        apellido: this.apellido,
        identificacion: this.identificacion
      };

      axios.put('http://localhost:8090/api/v1/registro', usuario)
        .then(() => {
          alert('Usuario modificado correctamente');
        })
        .catch(error => {
          console.error('Error al modificar usuario:', error);
          alert('Error al modificar usuario');
        });
    }
  }
};
</script>


<style>
html {
  background-color: #56baed;
}

body {
  font-family: "Poppins", sans-serif;
  height: 100vh;
}

a {
  color: #92badd;
  display: inline-block;
  text-decoration: none;
  font-weight: 400;
}

h2 {
  text-align: center;
  font-size: 16px;
  font-weight: 600;
  text-transform: uppercase;
  display: inline-block;
  margin: 40px 8px 10px 8px;
  color: #cccccc;
}

/* STRUCTURE */
.wrapper {
  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: center;
  width: 100%;
  /* Ajusta el ancho al 100% del viewport */
  min-height: 100%;
  padding: 20px;
}

#formContent {
  -webkit-border-radius: 10px;
  border-radius: 10px;
  background: #fff;
  padding: 30px;
  width: 90%;
  /* Ancho del formulario ajustado al 90% del contenedor */
  max-width: 600px;
  /* Establece un ancho máximo para el formulario */
  position: relative;
  padding: 0px;
  -webkit-box-shadow: 0 30px 60px 0 rgba(0, 0, 0, 0.3);
  box-shadow: 0 30px 60px 0 rgba(0, 0, 0, 0.3);
  text-align: center;
}

#formFooter {
  background-color: #f6f6f6;
  border-top: 1px solid #dce8f1;
  padding: 25px;
  text-align: center;
  -webkit-border-radius: 0 0 10px 10px;
  border-radius: 0 0 10px 10px;
}

/* TABS */
h2.inactive {
  color: #cccccc;
}

h2.active {
  color: #0d0d0d;
  border-bottom: 2px solid #5fbae9;
}

/* FORM TYPOGRAPHY*/
input[type=button],
input[type=submit],
input[type=reset] {
  background-color: #56baed;
  border: none;
  color: white;
  padding: 15px 80px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  text-transform: uppercase;
  font-size: 13px;
  -webkit-box-shadow: 0 10px 30px 0 rgba(95, 186, 233, 0.4);
  box-shadow: 0 10px 30px 0 rgba(95, 186, 233, 0.4);
  -webkit-border-radius: 5px;
  border-radius: 5px;
  margin: 5px 20px 40px 20px;
  -webkit-transition: all 0.3s ease-in-out;
  -moz-transition: all 0.3s ease-in-out;
  -ms-transition: all 0.3s ease-in-out;
  -o-transition: all 0.3s ease-in-out;
  transition: all 0.3s ease-in-out;
}

input[type=button]:hover,
input[type=submit]:hover,
input[type=reset]:hover {
  background-color: #39ace7;
}

input[type=button]:active,
input[type=submit]:active,
input[type=reset]:active {
  -moz-transform: scale(0.95);
  -webkit-transform: scale(0.95);
  -o-transform: scale(0.95);
  -ms-transform: scale(0.95);
  transform: scale(0.95);
}

input[type=text],
input[type=password] {
  background-color: #f6f6f6;
  border: none;
  color: #0d0d0d;
  padding: 15px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 5px;
  width: calc(80% - 95px);
  /* Ancho de los campos de entrada ajustado */
  border: 2px solid #f6f6f6;
  -webkit-transition: all 0.5s ease-in-out;
  -moz-transition: all 0.5s ease-in-out;
  -ms-transition: all 0.5s ease-in-out;
  -o-transition: all 0.5s ease-in-out;
  transition: all 0.5s ease-in-out;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}

input[type=text]:focus,
input[type=password]:focus {
  background-color: #fff;
  border-bottom: 2px solid #5fbae9;
}

input[type=text]::placeholder,
input[type=password]::placeholder {
  color: #cccccc;
}

/* ANIMATIONS */
/* Simple CSS3 Fade-in-down Animation */
.fadeInDown {
  -webkit-animation-name: fadeInDown;
  animation-name: fadeInDown;
  -webkit-animation-duration: 1s;
  animation-duration: 1s;
  -webkit-animation-fill-mode: both;
  animation-fill-mode: both;
}

@-webkit-keyframes fadeInDown {
  0% {
    opacity: 0;
    -webkit-transform: translate3d(0, -100%, 0);
    transform: translate3d(0, -100%, 0);
  }

  100% {
    opacity: 1;
    -webkit-transform: none;
    transform: none;
  }
}

@keyframes fadeInDown {
  0% {
    opacity: 0;
    -webkit-transform: translate3d(0, -100%, 0);
    transform: translate3d(0, -100%, 0);
  }

  100% {
    opacity: 1;
    -webkit-transform: none;
    transform: none;
  }
}

/* Simple CSS3 Fade-in Animation */
@-webkit-keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

@-moz-keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

.fadeIn {
  opacity: 0;
  -webkit-animation: fadeIn ease-in 1;
  -moz-animation: fadeIn ease-in 1;
  animation: fadeIn ease-in 1;

  -webkit-animation-fill-mode: forwards;
  -moz-animation-fill-mode: forwards;
  animation-fill-mode: forwards;

  -webkit-animation-duration: 1s;
  -moz-animation-duration: 1s;
  animation-duration: 1s;
}

.fadeIn.first {
  -webkit-animation-delay: 0.4s;
  -moz-animation-delay: 0.4s;
  animation-delay: 0.4s;
}

.fadeIn.second {
  -webkit-animation-delay: 0.6s;
  -moz-animation-delay: 0.6s;
  animation-delay: 0.6s;
}

.fadeIn.third {
  -webkit-animation-delay: 0.8s;
  -moz-animation-delay: 0.8s;
  animation-delay: 0.8s;
}

.fadeIn.fourth {
  -webkit-animation-delay: 1s;
  -moz-animation-delay: 1s;
  animation-delay: 1s;
}

/* Simple CSS3 Fade-in Animation */
.underlineHover:after {
  display: block;
  left: 0;
  bottom: -10px;
  width: 0;
  height: 2px;
  background-color: #56baed;
  content: "";
  transition: width 0.2s;
}

.underlineHover:hover {
  color: #0d0d0d;
}

.underlineHover:hover:after {
  width: 100%;
}

/* OTHERS */
*:focus {
  outline: none;
}
</style>
