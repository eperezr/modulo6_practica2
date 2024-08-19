<template>
    <div v-if="tickedDatos">
      <h1>Kardex de soportes {{tickedId}}</h1>
      <div>
        <h2>Información del Dueño</h2>
        <p>Nombre: {{ tickedDatos?.cliente.nombre }}</p>
        <p>Dirección: {{ tickedDatos.cliente.direccion }}</p>
        <p>Teléfono: {{ tickedDatos.cliente.telefono }}</p>
      </div>
      <div>
        <h2>Información de la Mascota</h2>
        <p>Solicitud: {{ tickedDatos.solicitud }}</p>
        <p>Sistema: {{ tickedDatos.sistema }} </p>
        <p>Marca: {{ tickedDatos.marca }}</p>
      </div>
      <div>
        <h2>Soportes Realizados</h2>
        <table>
          <thead>
            <tr>
              <th>Solucion</th>
              <th>Fecha de Aplicación</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="soporte in listaSoportes" :key="soporte.id">
              <td>{{ soporte.soporte.nombre }}</td>
              <td>{{ soporte.fecha }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </template>
  
  <script>
  import { mapState, mapGetters, mapActions } from 'vuex'
  export default {
    name: 'reporteSoporteView',
    data() {
      return {
        owner: {
          name: 'Juan Pérez',
          address: '123 Calle Falsa',
          phone: '555-1234'
        },
        pet: {
          name: 'Firulais',
          species: 'Perro',
          breed: 'Labrador',
        },
        tickedDatos:null,
        listaSoportes:[],
        vaccines: [
          { id: 1, name: 'Rabia', date: '2022-01-15', veterinarian: 'Dr. Smith' },
          { id: 2, name: 'Parvovirus', date: '2022-02-20', veterinarian: 'Dr. Doe' },
          { id: 3, name: 'Moquillo', date: '2022-03-10', veterinarian: 'Dr. Brown' }
        ]
      };
    },
    methods: {
      getTicked(){
        const tickedId = this.$route.params.id;
        const vm = this;
        this.axios.get(this.getBaseUrl+"/mascotas/"+tickedId+"?_expand=cliente")
        .then(function(response){
          vm.tickedDatos = response.data;
        })
        .catch(function(error){
          console.error(error);
        })
      },
      getSoporte(){
        const tickedId = this.$route.params.id;
        const vm = this;
        this.axios.get(this.getBaseUrl+"/tickedSoporte?_expand=soporte&tickedId="+tickedId)
        .then(function(response){
          vm.listaSoportes = response.data;
        })
        .catch(function(error){
          console.error(error);
        })
      }
    },
    computed: {
      ...mapGetters(['getBaseUrl']),
        tickedId() {
        // Recupera el parámetro de ruta 'id'
        return this.$route.params.id;
      }
    },
    mounted(){
      this.getTicked();
      this.getSoporte();
    }
  };
  </script>
  
  <style scoped>
  table {
    width: 100%;
    border-collapse: collapse;
  }
  th, td {
    border: 1px solid #ddd;
    padding: 8px;
  }
  th {
    background-color: #f2f2f2;
  }
  </style>
  