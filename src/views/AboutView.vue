<template>
  <div>
    <!--<img :src="logo" alt="Logo">-->
      <h1>Gestión de Empresas</h1>
      <form @submit.prevent="addClient">
        <input v-model="newEmpresa.param1" type="text" placeholder="Nombre Empresa" required /> <br><br>
        <input v-model.number="newEmpresa.param2" type="number" placeholder="Cantidad Empleados" required /> <br><br>
        <input v-model.number="newEmpresa.param3" type="number" placeholder="Cantidad proyectos" required /> <br><br>
        <button type="submit">Añadir Empresa</button>
      </form>
      <div>
        <table>
        <thead>
          <tr>
            <th>Empresa   </th>
            <th>Cantidad Empleados   </th>
            <th>Cantidad de proyectos   </th>
            <th>ACCIONES</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="client in clients" :key="client.id">
            <td>{{ client.param1 }}</td>
            <td>{{ client.param2 }}</td>
            <td>{{ client.param3 }}</td>
            <td>
              <button @click="editEmpresa(client)">Editar</button>
              <button @click="deleteEmpres(client.idcod)">Eliminar</button>
            </td>
          </tr>
        </tbody>
      </table>
      </div>
     <div v-if="editingClient">
      <h2>Editar Cliente</h2>
      <!--<form>
        <input id="inputNombre" v-model="editedClient.param1" type="text" placeholder="Empresa" required /> <br><br>
        <input id="inputEdad" v-model.number="editedClient.param2" type="number" placeholder="Empleados" required /> <br><br>
        <input id="InputExperiencia" v-model.number="editedClient.param3" type="number" placeholder="Proyectos" required /> <br><br>
        <button type="submit" @click="editEmpresa()">Actualizar Cliente</button>
        <button type="button" @click="cancelEdit">Cancelar</button>
      </form>-->
    </div>
   </div>
</template>

<script>
/* eslint-disable */
//import logo from '@assets/empresa_logo.png'
import axios from 'axios'
export default {
  data () {
    return {
      //logo,
      clients: [],
      newEmpresa: {
        param1: '',
        param2: null,
        param3: null
      },
      editingClient: null,
      editedClient: {
        param1: '',
        param2: null,
        param3: null,
      },
      errorMessage: ''
    }
  },
  mounted () {
    this.fetchEmpres()
  },
  methods: {
    async fetchEmpres () {
      try {
        const response = await axios.get('https://api.yumserver.com/15357/generic/empresa')
        this.clients = response.data
      } catch (error) {
        this.errorMessage = 'Error al obtener empresas: ' + error.message
      }
    },
    async addEmpres () {
      try {
        const response = await axios.post('https://api.yumserver.com/15357/generic/empresa', this.newEmpresa,
        { headers: { 'Content-Type': 'application/json' } })
        this.clients.push(response.data)
        this.resetForm()
      } catch (error) {
        this.errorMessage = 'Error al añadir empresa: ' + error.message
      }
    },
    /*editEmpres(client) {
      this.editingClient = true;
      this.editedClient = { ...client };
    },*/

    async editEmpresa(client) {
    const nuevoNombre = prompt("Nueva Empresa:", client.param1);
    const nuevaEdad = prompt("Cantidad Empleados:", client.param2);
    const nuevosAniosExperiencia = prompt("Cantidad Proyectos", client.param3);

    if (nuevoNombre && nuevaEdad && nuevosAniosExperiencia) {
      const clienteActualizado = {
        idcod: client.idcod,
        param1: nuevoNombre,
        param2: parseInt(nuevaEdad),
        param3: parseInt(nuevosAniosExperiencia)
      };

      try {
        await axios.patch(`https://api.yumserver.com/15357/generic/empresa`, clienteActualizado);
        await this.fetchEmpres();
        alert('Empresa actualizada correctamente.');
      } catch (error) {
        console.error("Error al editar la empresa:", error);
        alert('No se pudo editar  la empresa.');
      }
    }
  },
    cancelEdit() {
      this.editingClient = false;
      this.editedClient = { param1: '', param2: null, param3: null };
    },
    async deleteEmpres (clientId) {

      try {
        const confirmacion = confirm('¿Seguro?');
      if (!confirmacion){
        return;
      }
        await axios.delete(`https://api.yumserver.com/15357/generic/empresa`,
          {data: {
            idcod: clientId
        }
      })
        this.clients = this.clients.filter(client => client.idcod !== clientId)
      } catch (error) {
        this.errorMessage = 'Error al eliminar cliente: ' + error.message
      }
    }
  }

}
</script>
<style>
td{
  background:  black;
  color:rgb(85, 22, 107);
  padding: 5px;
  border: 1px solid rgba(250, 201, 136, 0);
}
th{
  color:rgb(85, 22, 107);
  background:  black;
  padding: 7px;
}
body{
  background: rgb(1, 1, 51);
}
button{
  margin: 5px;
  padding: 5px;
  border: 1px solid black;
  background:  black;
  color:rgb(85, 22, 107);
}
h1{
  color:rgb(85, 22, 107);
}
table{
  margin: auto;
  width: 50%;
  border-collapse: collapse;
}
/*img{
  width: 125px;
    border-radius: 100%;
    position: relative;
    top: -10px;
    transition: all 1s;
    transform: scale(1);
    filter: saturate(1);
    padding: 10px;
    margin: 15px;
}
#img1:hover{
    filter: saturate(1.5);
    transform: scale(0.9);
}*/
</style>
