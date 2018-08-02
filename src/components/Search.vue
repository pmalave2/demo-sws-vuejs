<template>
  <div class="search">
    <h1 class="text-center" v-show="false">{{ msg }}</h1>

    <div class="modal fade" id="modalCenter" tabindex="-1" role="dialog" aria-labelledby="modalCenterTitle" aria-hidden="true">
      <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="modalCenterTitle">{{ p.name }}</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
              <div class="container-fluid text-capitalize">
                <div class="row">
                  <div class="col-6 text-right">altura</div>
                  <div class="col-6">{{ p.height }}</div>
                </div>
                <div class="row">
                  <div class="col-6 text-right">peso</div>
                  <div class="col-6">{{ p.mass }}</div>
                </div>
                <div class="row">
                  <div class="col-6 text-right">año nacimiento</div>
                  <div class="col-6">{{ p.birth_year }}</div>
                </div>
                <div class="row">
                  <div class="col-6 text-right">genero</div>
                  <div class="col-6">{{ p.gender }}</div>
                </div>
                <div class="row">
                  <div class="col-6 text-right">planeta</div>
                  <div class="col-6">{{ p.planeta }}</div>
                </div>
                <div class="row">
                  <div class="col-6 text-right">films</div>
                  <div class="col-6">
                    <ul>
                      <li v-for="(z, index) in p.filmsName" :key="index">{{ z }}</li>
                    </ul>                    
                  </div>
                </div>
                <div class="row">
                  <div class="col-6" v-for="(img, index) in p.imagenes" :key="index">
                    <div class="card border-secondary">
                      <img class="card-img" v-bind:src="img.thumbnailUrl" alt="Card image">
                    </div>
                  </div>
                </div>  
              </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="row sb">
        <div class="col"></div>
        <div class="col-auto">
          <p>
            <input type="text" v-model="name" placeholder="Nombre" @keyup="buscar()">
            <input value="Buscar" type="button" @click="buscar()">
          </p>
        </div>
        <div class="col"></div>
      </div>

      <ul v-if="errors && errors.length">
        <li v-for="(error, index) of errors" :key="index">
          {{error.message}}
        </li>
      </ul>

      <div class="row">
        <div class="col-3 cc" v-for="(x, index) in swp" :key="index">
          <div class="card border-secondary">
              <div class="card-body">
                <h5 class="card-title">{{ x.name }}</h5>
                <p class="card-text">

                </p>
                <a href="#" class="btn btn-primary" @click="getP(x)">Ir al detalle</a>
              </div>
          </div>
        </div>
      </div>
    </div>
    <p v-show="false">{{msg}}</p>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    name: 'Search',
    props: {
      msg: String
    },
    data: function (){
      return {
        name: 'Skywalker',
        swp: [],
        p: {
          name: '',
          height: '',
          mass: '',
          birth_year: '',
          gender: '',
          planeta: '',
          imagenes: [
            {thumbnailUrl: 'https://upload.wikimedia.org/wikipedia/commons/6/6c/Star_Wars_Logo.svg'}
          ],
          filmsName: [],
        },
        baseUrl: 'https://swapi.co/api/people/',
        errors: []
      }
    },
    created() {
      this.buscar();
    },
    methods:{
        buscar: function () {
          axios.get(this.baseUrl, {params:{search:this.name}})
            .then(response => {
              this.swp = response.data.results;
            })
            .catch(e => {
              this.errors.push(e)
            })
        },
        getP: function(e){
          this.p.name = e.name;
          this.p.height = e.height;
          this.p.mass = e.mass;
          this.p.birth_year = e.birth_year;
          this.p.gender = e.gender;
          this.addPlanetName(e, this.p);
          this.p.filmsName = this.addFilmsName(e);
          //addImages($scope.p);
          // eslint-disable-next-line
          $('#modalCenter').modal('show');
        },
        addFilmsName: function(e) {
          var filmsName = [];
          e.films.forEach(e => {
            axios.get(e)
            .then(response => {
              filmsName.push(response.data.title);
            })
            .catch(e => {
              filmsName.push('Nada');
              this.errors.push(e);
            });		
          });
          return filmsName;
        },
        addPlanetName: function(e, p) {
          axios.get(e.homeworld)
            .then(response =>{
              p.planeta = response.data.name;
            }) 
            .catch(e => {
              p.planeta = 'Nada';
              this.errors.push(e);
            });
        }
    }
  };

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .sb {
    padding-top: 2em;
  }
  .cc {
    padding-bottom: 1em;
  }
</style>
