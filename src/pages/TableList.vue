<template>
  <div class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-12">
          <card class="strpied-tabled-with-hover" body-classes="table-full-width table-responsive">
            <template slot="header">
              <h4 class="card-title">Tabla de Ropa</h4>
              <p class="card-category">Lista de artículos de ropa</p>
            </template>
            <l-table class="table-hover table-striped"
                     :columns="table1.columns"
                     :data="table1.data"
                     @edit="openEditModal">
            </l-table>
            <button class="btn btn-primary" @click="openEditModal(null)">Añadir Ropa</button>
          </card>
        </div>

        <div v-if="isModalOpen" class="modal fade show" style="display: block;">
          <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title">Editar Ropa</h5>
                <button type="button" class="close" @click="closeEditModal">&times;</button>
              </div>
              <div class="modal-body">
                <div class="form-group">
                  <label>ID</label>
                  <input type="text" class="form-control" v-model="editItem.id" disabled />
                </div>
                <div class="form-group">
                  <label>Nombre</label>
                  <input type="text" class="form-control" v-model="editItem.nombre" />
                </div>
                <div class="form-group">
                  <label>Marca</label>
                  <input type="text" class="form-control" v-model="editItem.marca" />
                </div>
                <div class="form-group">
                  <label>Talla</label>
                  <input type="text" class="form-control" v-model="editItem.talla" />
                </div>
                <div class="form-group">
                  <label>Color</label>
                  <input type="text" class="form-control" v-model="editItem.color" />
                </div>
                <div class="form-group">
                  <label>Precio</label>
                  <input type="number" class="form-control" v-model="editItem.precio" />
                </div>
                <div class="form-group">
                  <label>Existencia</label>
                  <input type="number" class="form-control" v-model="editItem.existencia" />
                </div>
              </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-secondary" @click="closeEditModal">Cerrar</button>
                <button type="button" class="btn btn-primary" @click="updateItem">Actualizar</button>
              </div>
            </div>
          </div>
        </div>

        <div v-if="isModalOpen" class="modal-backdrop fade show"></div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import LTable from 'src/components/Table.vue';
import Card from 'src/components/Cards/Card.vue';

const tableColumns = ['ID', 'Nombre', 'Marca', 'Talla', 'Color', 'Precio', 'Existencia'];

export default {
  components: {
    LTable,
    Card
  },
  data() {
    return {
      table1: {
        columns: [...tableColumns],
        data: []
      },
      isModalOpen: false,
      editItem: {
        id: '',
        nombre: '',
        marca: '',
        talla: '',
        color: '',
        precio: '',
        existencia: ''
      }
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      try {
        const response = await axios.get('http://localhost:8000/ropas');
        this.table1.data = response.data;
      } catch (error) {
        console.error('Error al obtener los datos:', error);
      }
    },
    openEditModal(item) {
      if (item) {
        this.editItem = { ...item };
      } else {
        this.editItem = {
          id: '',
          nombre: '',
          marca: '',
          talla: '',
          color: '',
          precio: '',
          existencia: ''
        };
      }
      this.isModalOpen = true;
    },
    closeEditModal() {
      this.isModalOpen = false;
    },
    async updateItem() {
      try {
        await axios.put(`http://localhost:8000/ropaupdate/${this.editItem.id}`, this.editItem);
        this.closeEditModal();
        this.fetchData();
      } catch (error) {
        console.error('Error al actualizar los datos:', error);
      }
    }
  }
}
</script>

<style>
.modal {
  display: none;
  position: center;
}
.modal.show {
  display: block;
  position: center;
}
.modal-backdrop {
  position: center;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
}
</style>
