<template>
  <div class="search">
    <br>
    <h1 class="text-center">{{ msg }}</h1>

    <b-modal 
      id="modalCenter"
      :title="p.data.name"
      ok-only
    >
      <b-container fluid class="text-capitalize">
        <b-row>
          <b-col cols="6" class="text-right">altura</b-col>
          <b-col cols="6">{{ p.data.height }}</b-col>
        </b-row>
        <b-row>
          <b-col cols="6" class="text-right">peso</b-col>
          <b-col cols="6">{{ p.data.mass }}</b-col>
        </b-row>
        <b-row>
          <b-col cols="6" class="text-right">año nacimiento</b-col>
          <b-col cols="6">{{ p.data.birth_year }}</b-col>
        </b-row>
        <b-row>
          <b-col cols="6" class="text-right">genero</b-col>
          <b-col cols="6">{{ p.data.gender }}</b-col>
        </b-row>
        <b-row>
          <b-col cols="6" class="text-right">planeta</b-col>
          <b-col cols="6">
            <b-spinner label="Loading..." class="text-center" v-show="!planetaShow"></b-spinner>
            <span v-show="planetaShow" v-text="p.planeta"></span>
          </b-col>
        </b-row>
        <b-row>
          <b-col cols="6" class="text-right">films</b-col>
          <b-col cols="6">
            <b-spinner label="Loading..." class="text-center" v-show="!filmsNameShow"></b-spinner>
            <ul class="text-left list-unstyled" v-show="filmsNameShow">
              <li
                v-for="(filmName, index) in p.filmsName" 
                :key="index"
              ><span v-text="filmName"></span></li>
            </ul>                    
          </b-col>
        </b-row>
        <b-row>
          <b-col
            cols="6" 
            v-for="(img, index) in p.imagenes" 
            :key="index"
          >
            <div class="card border-secondary">
              <img 
                class="card-img" 
                v-bind:src="img.thumbnailUrl" 
                alt="Card image"
              >
            </div>
          </b-col>
        </b-row>  
      </b-container>

      <template slot="modal-footer" slot-scope="{ ok, cancel, hide }">
        <b-button 
          variant="secondary" 
          @click="ok()"
        >
          Close
        </b-button>
      </template>
    </b-modal>

    <b-container>
      <b-row class="sb">
        <b-col></b-col>
        <b-col cols="auto">
          <b-input-group>
            <b-form-input 
              type="text" 
              v-model="name" 
              placeholder="Nombre"
            ></b-form-input>
            <b-input-group-append>
              <b-button 
                variant="outline-secondary" 
                @click="buscar()"
              >Buscar</b-button>
            </b-input-group-append>
          </b-input-group>
        </b-col>
        <b-col></b-col>
      </b-row>

      <ul v-if="errors && errors.length">
        <li 
          v-for="(error, index) of errors" 
          :key="index"
        >
          {{error.message}}
        </li>
      </ul>

      <b-row>
        <b-col 
          cols="3"
          class="cc" 
          v-for="(x, index) in swp" 
          :key="index"
        >
          <b-card
            border-variant="secondary"
            :title="x.name"
          >
            <b-card-text>
              <b-button
                variant="primary" 
                href="#" 
                @click="getP(x)"
              >Ir al detalle</b-button>
            </b-card-text>
          </b-card>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'Search',
    props: {
      msg: String
    },
    data: () => {
      return {
        name: '',
        swp: [],
        p: {
          data: {},
          imagenes: [
            {
              thumbnailUrl: 'https://upload.wikimedia.org/wikipedia/commons/6/6c/Star_Wars_Logo.svg'
            }
          ],
          planeta: '',
          filmsName: [],
        },
        baseUrl: 'https://swapi.co/api/people/',
        errors: [],
        filmsNameShow: false,
        planetaShow: false
      }
    },
    created: function () {
      this.buscar();
    },
    methods: {
        buscar: function () {
          axios.get(
            this.baseUrl, 
            {
              params: {
                search:this.name
              }
            }
          )
          .then(response => {
            this.swp = response.data.results;
          })
          .catch(e => {
            this.errors.push(e)
          })
        },
        getP: function(e) {
          this.p.data = e;
          this.addPlanetName(e.homeworld);
          this.addFilmsName(e.films);
          //addImages($scope.p);
          this.showModal('modalCenter')
        },
        addFilmsName: function(e) {
          this.p.filmsName = []
          let cont = 0;
          this.filmsNameShow = false;

          e.forEach((e, i, a) => {
            axios.get(e)
            .then(response => {
              this.p.filmsName.push(response.data.title)

              if (cont == a.length - 1)
                this.filmsNameShow = true;

              cont++;
            })
            .catch(e => {
              this.p.filmsName.push('Nada');
              this.errors.push(e);
            });		
          });
        },
        addPlanetName: function(e) {
          this.p.planeta = []
          this.planetaShow = false;

          axios.get(e)
            .then(response => {
              this.p.planeta = response.data.name;

              this.planetaShow = true;
            }) 
            .catch(e => {
              this.p.planeta = 'Nada';
              this.errors.push(e);
            });
        },
        showModal: function(id) {
          this.$bvModal.show(id)
        }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  .sb {
    padding: 2em 0;
  }
  .cc {
    padding-bottom: 1em;
  }
  [v-cloak] {
    display: none;
  }
</style>
