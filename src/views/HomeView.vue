<template>
  <div>
    <h1>Gestión de Clientes</h1>
    <form @submit.prevent="addClient">
      <input v-model="newClient.param1" type="text" placeholder="Nombre" required /> <br><br>
      <input v-model.number="newClient.param2" type="number" placeholder="Edad" required /> <br><br>
      <input v-model.number="newClient.param3" type="number" placeholder="Años de experiencia" required /> <br><br>
      <button type="submit">Añadir Cliente</button>
    </form>

    <div>
      <table>
        <thead>
          <tr>
            <th>Nombre </th>
            <th>Edad </th>
            <th>Años de experiencia </th>
            <th>Fecha de ingreso </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="client in clients" :key="client.idcod">
            <td>{{ client.param1 }}</td>
            <td>{{ client.param2 }}</td>
            <td>{{ client.param3 }}</td>
            <td>
              <button @click="editClient(client)">Editar </button>
              <button @click="deleteClient(client.idcod)">Eliminar</button>
            </td>
          </tr>
        </tbody>
      </table>
      <!-- <div v-if="editingClient">
      <h2>Editar Cliente</h2>
      <form @submit.prevent="updateClient">
        <input v-model="editedClient.param1" type="text" placeholder="Nombre" required /> <br><br>
        <input v-model.number="editedClient.param2" type="number" placeholder="Edad" required /> <br><br>
        <input v-model.number="editedClient.param3" type="number" placeholder="Años de experiencia" required /> <br><br>
        <button type="submit" >Actualizar Cliente</button>
        <button type="button" @click="cancelEdit">Cancelar</button>
      </form>
    </div> -->
    </div>
  </div>
</template>

<script>
import axios from 'axios'
/* eslint-disable */
export default {
  data () {
    return {
      clients: [],
      newClient: {
        param1: '',
        param2: null,
        param3: null
      },
      editingClient: null,
      editedClient: {
        param1: '',
        param2: null,
        param3: null
      },
      errorMessage: ''
    }
  },
  mounted () {
    this.fetchClients()
  },
  methods: {
    async fetchClients () {
      try {
        const response = await axios.get('https://api.yumserver.com/15357/generic/clientes')
        this.clients = response.data
      } catch (error) {
        this.errorMessage = 'Error al obtener clientes: ' + error.message
      }
    },
    async addClient () {
      try {
        const response = await axios.post('https://api.yumserver.com/15357/generic/clientes', this.newClient,
        { headers: { 'Content-Type': 'application/json' } })
        this.clients.push(response.data)
        this.resetForm()
      } catch (error) {
        this.errorMessage = 'Error al añadir cliente: ' + error.message
      }
    },
    async editClient(client) {
    const nuevoNombre = prompt("Ingrese el nuevo nombre:", client.param1);
    const nuevaEdad = prompt("Ingrese la nueva edad:", client.param2);
    const nuevosAniosExperiencia = prompt("Ingrese los años de experiencia:", client.param3);

    if (nuevoNombre && nuevaEdad && nuevosAniosExperiencia) {
      const clienteActualizado = {
        idcod: client.idcod,
        param1: nuevoNombre,
        param2: parseInt(nuevaEdad),
        param3: parseInt(nuevosAniosExperiencia)
      };

      try {
        await axios.patch(`https://api.yumserver.com/15357/generic/clientes`, clienteActualizado);
        await this.fetchClients();
        alert('Cliente actualizado correctamente.');
      } catch (error) {
        console.error("Error al editar el cliente:", error);
        alert('No se pudo editar el cliente.');
      }
    }
  },
  cancelEdit() {
    this.editingClient = false;
    this.editedClient = { param1: '', param2: null, param3: null };
  },
    async deleteClient(clientId) {
      const confirmacion = confirm('¿Seguro que quieres eliminar este cliente?');
      if (!confirmacion) {
        return;
      }
      try {
        await axios.delete(`https://api.yumserver.com/15357/generic/clientes`, {
          data: { idcod: clientId }
        });
        this.clients = this.clients.filter(client => client.idcod !== clientId);
      } catch (error) {
        this.errorMessage = 'Error al eliminar cliente: ' + error.message;
      }
    },
    resetForm() {
      this.newClient = { param1: '', param2: null, param3: null };
    }
  }
}
</script>
