<template>
  <div>
    <h1>Registrar Ticked de Soporte</h1>
    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="cliente">Cliente:</label>
        <select id="cliente" v-model="form.clienteId" :class="{ 'is-invalid': errors.clienteId }">
          <option :value="cliente.id" v-for="(cliente, index) in clienteList" :key="`cliente-${index}`">{{ cliente.solicitud }}
          </option>
        </select>
        <div v-if="errors.clienteId" class="invalid-feedback">{{ errors.clienteId }}</div>
      </div>

      <div class="form-group">
        <label for="name">Solicitud:</label>
        <input type="text" id="name" v-model="form.solicitud" :class="{ 'is-invalid': errors.solicitud }"
          placeholder="Detalle de solicitud de Soporte" />
        <div v-if="errors.solicitud" class="invalid-feedback">{{ errors.solicitud }}</div>
      </div>

      <div class="form-group">
        <label for="sistema">Sistema Operativo:</label>
        <select id="sistema" v-model="form.sistema" :class="{ 'is-invalid': errors.sistema }">
          <option :value="sistema" v-for="(sistema, index) in sistemaList" :key="`sistema-${index}`">{{ sistema }}</option>
        </select>
        <div v-if="errors.sistema" class="invalid-feedback">{{ errors.sistema }}</div>
      </div>

      <div class="form-group">
        <label for="marca">Marca:</label>
        <input type="text" id="marca" v-model="form.marca" :class="{ 'is-invalid': errors.marca }"
          placeholder="Ingrese marca o modelo del equipo del cliente" />
        <div v-if="errors.marca" class="invalid-feedback">{{ errors.marca }}</div>
      </div>

      <button type="submit" class="btn btn-primary">Registrar</button>
    </form>
  </div>
</template>
  
<script>
import { mapState, mapGetters, mapActions } from 'vuex'
export default {
  name: 'MascotaNew',
  data() {
    return {
      clienteList: [],
      sistemaList: [
        "Windows",
        "Linux",
        "MAC"
      ],
      form: {
        solicitud: '',
        sistema: '',
        marca: '',
        clienteId: null
      },
      errors: {}
    };
  },
  methods: {
    ...mapActions(['increment']),
    validateForm() {
      this.errors = {};

      if (!this.form.solicitud) {
        this.errors.solicitud = 'La solicitud es necesaria para proceder con el Soporte.';
      }

      if (!this.form.sistema) {
        this.errors.sistema = 'La sistema es obligatorio.';
      }

      if (!this.form.marca) {
        this.errors.marca = 'La marca es necesaria.';
      }

      if (!this.form.clienteId) {
        this.errors.clienteId = 'El Cliente es obligatorio.';
      }

      return Object.keys(this.errors).length === 0;
    },

    submitForm() {
      if (this.validateForm()) {
        // Enviar los datos al servidor
        this.save();
        // Reiniciar el formulario
        this.form = {
          solicitud: '',
          sistema: '',
          marca: '',
          clienteId: null
        };
      }
    },
    save() {
      const vm = this;
      this.axios.post(this.baseUrl + "/mascotas", this.form)
        .then(function (response) {
          if (response.status == '201') {
            vm.$emit('on-register', response.data);
          }
          vm.itemList = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    },
    getClienteList() {
      const vm = this;
      this.axios.get(this.baseUrl + "/clientes")
        .then(function (response) {
          vm.clienteList = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    },
  },
  computed: {
    // propiedades computadas que dependen de otras propiedades reactivas
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl
    }
  },
  mounted() {
    this.getClienteList();
  },
}
</script>
  
<style scoped></style>
  