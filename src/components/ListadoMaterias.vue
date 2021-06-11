<template>
<v-img src="https://cdn.wallpapersafari.com/8/23/NKZOwi.jpg" gradient="to top right, rgba(100,115,201,.33), rgba(25,32,72,.7)" >
  <v-container>
      
    <v-row no-gutters >
      <v-col cols="12" >
        <v-data-table style="border-radius: 15px;margin-top: 4px;"
          :headers="headers"
          :items="desserts"
          :search="buscar"
          class="elevation-5 mr-1 ml-1 mb-1 mt-8"
        >
          <template v-slot:top>
            <!-- encabezado de la tabla -->
            <v-toolbar flat color style="border-radius: 15px">
              <v-toolbar-title>
                <h3 class="text-center font-weight-bold" style="color: #1A237E">Listado de Materias</h3>
              </v-toolbar-title>
              <v-spacer></v-spacer>
              <!-- input de busqueda(filtro) -->
              <v-text-field
                class="text-xs-center"
                v-model="buscar"
                append-icon="search"
                label="Búsqueda"
                single-line
                hide-details
                color="#1565C0"
              ></v-text-field>
              <v-spacer></v-spacer>

              <!-- formulario temporal -->
              <v-dialog
                v-model="dialog"
                max-width="500px"
                style="color: purple"
              >
                <template v-slot:activator="{ on }">
                  <v-btn color="#3F51B5" dark class="mb-2" v-on="on"
                    >Nueva Materia</v-btn
                  >
                </template>
                <v-card style="border-radius: 10px">
                  <v-form ref="form" :lazy-validation="lazy">
                    <v-card-title>
                      <span class="headline">{{ formTitle }}</span>
                    </v-card-title>
                    <v-card-text>
                      <v-container>
                        <v-row>
                          <v-col cols="12" sm="4" md="4" >
                            <v-text-field
                              v-model="aula.numero"
                              :rules="RolTexto"
                              label="Nombre de Materia"
                              required
                            ></v-text-field>
                          </v-col>
                          <v-col cols="12" sm="4" md="4" >
                            <v-text-field
                              v-model="aula.numero"
                              :rules="RolTexto"
                              label="Codigo de Materia"
                              required
                            ></v-text-field>
                          </v-col>                        
                        </v-row>
                      </v-container>
                    </v-card-text>
                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn
                        color="green darken-1"
                        text
                        @click="generar"
                        v-if="
                          aula.turno != '' &&
                          aula.numero != '' &&
                          aula.codigo == ''
                        "
                        >Generar Código</v-btn
                      >
                      <v-btn color="red darken-1" text @click="close"
                        >Cancelar</v-btn
                      >
                      <v-btn
                        color="blue darken-1"
                        text
                        @click="save"
                        v-if="aula.codigo != '' && aula.numero != '' && aula.turno != '' && aula.grado != '' && aula.seccion != ''"
                        >Guardar</v-btn
                      >
                    </v-card-actions>
                  </v-form>
                </v-card>
                <!-- Cierre formulario temporal -->
              </v-dialog>
            </v-toolbar>
          </template>

          <!-- Datos de la tabla -->
          <template v-slot:[`item.actions`]="{ item }">
            <v-icon small color="orange" class="mr-2" @click="editItem(item)"
              >mdi-pencil</v-icon
            >
            <v-icon small color="red" @click="deleteItem(item)"
              >mdi-delete</v-icon
            >
          </template>
          <template v-slot:no-data>
            <v-btn color="accent" @click="initialize">Refrescar</v-btn>
          </template>
        </v-data-table>
      </v-col>
    </v-row>
  </v-container>
</v-img>
</template>

<script>
import aulasService from "../services/aulasService";

export default {
  data: () => ({
    nombre: "",
    dialogG: false,
    items2: [
      "Primer Grado",
      "Segundo Grado",
      "Tercer Grado",
      "Cuarto Grado",
      "Quinto Grado",
      "Sexto Grado",
      "Septimo Grado",
      "Octavo Grado",
      "Noveno Grado",
    ],
    headersG: [
      {
        text: "Grado",
        sortable: false,
        value: "nombre",
      },
      { text: "Acciones", value: "actions", sortable: false },
    ],
    dessertsG: [],
    editedIndexG: -1,
    grado: {
      _id: null,
      nombre: "",
    },
     defaultItemG: {
      _id: null,
      nombre: "",
    },

    //datos de secciones
    dialogS: false,
    itemsS: ["A", "B", "C", "D"],
    headersS: [
      {
        text: "Seccion",
        sortable: false,
        value: "nombre",
      },
      { text: "Acciones", value: "actions", sortable: false },
    ],
    dessertsS: [],
    editedIndexS: -1,
    seccion: {
      _id: null,
      nombre: "",
    },
    defaultItemS: {
      _id: null,
      nombre: "",
    },
    //datos d3l aula
    lazy: "",
    buscar: "",
    _id: "",
    numero: "",
    turno: "",
    grado: "",
    seccion: "",
    codigo: "",
    RolTexto: [(v) => !!v || "Este campo es requerido"],
    dialog: false,
    grados: [],
    secciones: [],
    headers: [
      /* {
        text: "Codigo",
        align: "start",
        sortable: false,
        value: "_id"
      },*/
      {
        text: "Número",
        text: "Número de Aula",
        sortable: false,
        value: "numero",
      },
      {
        text: "Turno",
        sortable: false,
        value: "turno",
      },
      {
        text: "Código Aula",
        sortable: false,
        value: "codigo",
      },
      {
        text: "Grado",
        sortable: false,
        value: "grado.nombre",
      },
      {
        text: "Sección",
        sortable: false,
        value: "seccion.nombre",
      },
      { text: "Acciones", value: "actions", sortable: false },
    ],
    turnos: [
      {
        nombre: "Matutino",
        _id: 1,
      },
      {
        nombre: "Vespertino",
        _id: 2,
      },
      {
        nombre: "Nocturno",
        _id: 3,
      },
    ],
    desserts: [],
    editedIndex: -1,
    aula: {
      _id: null,
      numero: "",
      turno: "",
      codigo: "",
      grado: "",
      seccion: "",
    },
    defaultItem: {
      id: "",
      numero: "",
      turno: "",
      codigo: "",
      grado: "",
      seccion: "",
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Nueva Aula" : "Modificar Aula";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
  },

  created() {
    this.initialize();
    this.cargarGrados();
    this.cargarSecciones();
  },

  methods: {

    initialize() {
      var ca = []
      aulasService
        .index()
        .then((res) => {
          ca = res.data.aula;
          ca.map((card)=> {
            if(card.turno == 1){
              card.turno = "Matutino"
            }
            if(card.turno == 2){
              card.turno = "Vespertino"
            }
            if(card.turno == 3){
              card.turno = "Nocturno"
            }
          })
          this.desserts = Object.assign([this.editedIndex], ca);
          console.log(Object.assign(res.data.aula));
        })
        .catch((e) => {
          console.log(e);
        });
    },
    //__________________generar codigo________________________
    generar() {
      aulasService
        .codigoaleatorio()
        .then((res) => {
          console.log(res.data.codigo);
          this.aula.codigo = res.data.codigo;
        })
        .catch((e) => {
          alert("Error al generar código");
          console.log(e);
        });
    },
    //__________________________________________
    save() {
      var data = {
        _id: this.aula._id,
        numero: this.aula.numero,
        turno: this.aula.turno,
        codigo: this.aula.codigo,
        grado: this.aula.grado,
        seccion: this.aula.seccion,
      };
      console.log(data);
      this.$refs.form.reset();
      if (this.editedIndex >= 0) {
        //Object.assign(this.desserts[this.editedIndex], this.aula);

        //_______________________________actualizar________________________
        aulasService
          .update(data._id, data)
          .then((response) => {
            data._id = response.data._id;
            console.log(response.data);
            this.dialog = false;
            alert("Aula modificada con éxito");
            this.initialize();
          })
          .catch((e) => {
            alert("Error al modificar aula");
            console.log(e);
          });
      } else {
        //___________________________guardar____________________
        aulasService
          .create(data)
          .then((response) => {
            this.aula._id = response.data._id;
            console.log(response.data);
            this.dialog = false;
            alert("Aula creada con éxito");
            this.initialize();
          })
          .catch((e) => {
            alert("Error || Ya existe esta sección");
            console.log(e);
          });
      }
    },

    //_______________________________eliminar___________________________
    deleteItem(item) {
      const index = this.desserts.indexOf(item);
      confirm("¿Estás seguro de querer eliminar el registro?") &&
        this.desserts.splice(index, 1);
      aulasService
        .remove(item._id)
        .then((response) => {
          console.log(response.data);
          //this.$router.push({ name: "tutorials" });
          alert("Aula eliminada con éxito");
          this.initialize();
        })
        .catch((e) => {
          alert("Error al eliminar Aula");
          console.log(e);
        });
    },
    //__________________________________________
    //metdo editar
    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.aula = Object.assign({}, item);
      //console.log( Object.entries({},item.numero,item.turno,item.codigo,item.grado._id,item.seccion._id));
      this.dialog = true;
    },

    //metdo cerrar dialog
    close() {
      this.dialog = false;
      this.$refs.form.reset();
      this.$nextTick(() => {
        this.aula = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    cargarGrados() {
      aulasService
        .traerGrados()
        .then((res) => {
          this.grados = res.data.grados;
          console.log("grados", this.grados);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    cargarSecciones() {
      aulasService
        .traerSecciones()
        .then((res) => {
          this.secciones = res.data.seccion;
          console.log("secciones", this.secciones);
        })
        .catch((e) => {
          console.log(e);
        });
    },
  },
};
</script>
