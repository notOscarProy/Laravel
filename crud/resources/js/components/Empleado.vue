<template>
  <div id="app">
    <h1 class="text-center">Catálogo de empleados</h1>
    <button
      type="button"
      class="btn btn-primary"
      @click="
        editar = false;
        showModal();
      "
    >
      Nuevo empleado
    </button>
    <div class="modal" :class="{ mostrar: modal }">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">
              {{ tituloModal }}
            </h5>
            <button type="button" class="close" @click="hideModal">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <div>
              <label for="nombre"></label>
              <input
                v-model="empleado.nombre"
                type="text"
                id="nombre"
                class="form-control"
                placeholder="Nombre(s)"
              />
            </div>
            <div>
              <label for="apellido"></label>
              <input
                v-model="empleado.apellido"
                type="text"
                id="apellido"
                class="form-control"
                placeholder="Apellido"
              />
            </div>
            <div>
              <label for="edad"></label>
              <input
                v-model="empleado.edad"
                type="number"
                id="edad"
                class="form-control"
                placeholder="Edad"
              />
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" @click="hideModal">
              Cancelar
            </button>
            <button type="button" class="btn btn-primary" @click="fnNuevo()">
              Guardar
            </button>
          </div>
        </div>
      </div>
    </div>
    <br /><br />
    <table class="table table-hover table-dark">
      <thead class="thead-dark">
        <th>#</th>
        <th>Nombre</th>
        <th>Apellido</th>
        <th>Edad</th>
        <th colspan="2" class="text-center">Acciones</th>
      </thead>
      <tbody
        is="transition-group"
        name="list-empleados"
        @leave="leave"
        :css="false"
      >
        <tr
          v-for="(row, index) in empleados"
          class="list-empleado"
          :key="row.id"
        >
          <td>
            <div>{{ row.id }}</div>
          </td>
          <td>
            <div>{{ row.nombre }}</div>
          </td>
          <td>
            <div>{{ row.apellido }}</div>
          </td>
          <td>
            <div>{{ row.edad }}</div>
          </td>
          <td>
            <div>
              <button
                type="button"
                @click="
                  editar = true;
                  showModal(index, row);
                "
                class="btn"
                style="color: white; background-color: orange"
              >
                Editar
              </button>
            </div>
          </td>
          <td>
            <div>
              <button
                type="button"
                @click="fnBorrar(row.id, $event)"
                class="btn btn-danger"
              >
                Borrar
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import Velocity from "velocity-animate";
export default {
  name: "App",
  components: {
    // HelloWorld
  },
  data() {
    return {
      empleados: [],
      idEmpleado: 0,
      empleado: {
        nombre: "",
        apellido: "",
        edad: "",
      },
      tituloModal: "",
      modal: false,
      editar: false,
    };
  },
  mounted() {
    this.fnListar();
  },
  methods: {
    async fnBorrar(id, evt) {
      let index = this.empleados.findIndex((element) => element.id === id);
      this.empleados.splice(index, 1);
      const res = await axios.delete("empleados/" + id);
    },

    leave(el, done) {
      let divs = el.querySelectorAll("div");
      Velocity(
        divs,
        {
          opacity: "[0.9, 1]",
          transformPerspective: " [400, 400]",
          rotateX: "-15",
        },
        { duration: 400, complete: done, }
      );
    },
    async fnListar() {
      const res = await axios.get("empleados");
      this.empleados = res.data;
    },
    async fnNuevo() {
      if (this.editar) {
        const res = await axios.put(
          "empleados/" + this.idEmpleado,
          this.empleado
        );
      } else {
        console.log("Datos: ", this.empleado);
        const res = await axios.post("empleados", this.empleado);
      }
      this.hideModal();
      this.fnListar();
    },
    showModal(id, data = {}) {
      console.log(data);
      this.modal = true;

      if (this.editar) {
        this.tituloModal = "Editar empleado";

        this.idEmpleado = data.id;
        this.empleado.nombre = data.nombre;
        this.empleado.apellido = data.apellido;
        this.empleado.edad = data.edad;
      } else {
        this.tituloModal = "Nuevo empleado";

        this.idEmpleado = "";
        this.empleado.nombre = "";
        this.empleado.apellido = "";
        this.empleado.edad = "";
      }
    },
    hideModal() {
      this.modal = false;
    },
  },
};
</script>

<style>
.list-empleado td div {
  box-sizing: border-box;
  max-height: 40px;
  padding: 0em 0em;
  overflow: hidden;
}
.mostrar {
  display: list-item;
  opacity: 1;
  background: rgba(27, 49, 97, 0.795);
}
</style>
