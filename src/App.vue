<template>
  <div id="app">

    <header class="mb-5">
      <nav class="navbar navbar-expand-md mb-4">
        <div class="navbar-background">
          <div class="container">
            <a class="navbar-brand" href="#"><img src="./assets/img/logo.png"></a>
          </div>
        </div>
      </nav>
    </header>

    <main class="container">
      <h1 class="mb-5 pb-3 border-bottom">Painel de clientes</h1>

      <div class="d-flex justify-content-between mb-4">
        <h3>Clientes</h3>
        <button class="btn btn-primary">Novo cliente</button>
      </div>

      <div class="row">
        <div class="col-md-3 mb-3">
          <label for="search">Buscar</label>
          <div class="input-group mb-3">
            <input type="search" class="form-control" v-model="configs.filter" name="search" placeholder="Buscar por nome ou e-mail..." aria-label="Buscar por nome ou e-mail..." aria-describedby="search">
            <div class="input-group-append">
              <span class="input-group-text" id="search"><i class="fa fa-search" aria-hidden="true"></i></span>
            </div>
          </div>
        </div>
      </div>

      <div class="table-responsive mb-4">
        <table class="table table-striped">
          <tbody>
            <tr v-for="user in filteredList" v-bind:key="user.id">
              <td class="d-flex align-items-stretch">
                <i class="fa fa-user-circle" aria-hidden="true"></i>
                <div>
                  <a href=""><strong>{{ user.name }}</strong></a>
                  <div>{{ user.contact }}</div>
                </div>
              </td>
              <td>
                <div class="d-flex align-items-center">
                  <span class="status-label" v-bind:class="user.status.type.toLowerCase()"></span>
                  {{ user.status.description }}
                </div>
              </td>
              <td><button class="btn"><i class="fa fa-cog" aria-hidden="true"></i></button></td>
            </tr>
          </tbody>
        </table>
      </div>

      <p class="lead" v-show="filteredList.length === 0">Nenhum cliente encontrado. <a href="javascript:void(0)" @click="configs.filter = ''" class="text-danger"><strong>Limpe sua busca</strong></a> e tente novamente.</p>

      <div class="pagination-filter d-flex justify-content-between flex-column flex-md-row align-items-center" v-if="filteredList.length">

        <div class="filter-view">
          <p>Exibindo <strong>{{ filteredList.length }}</strong> de resultados <strong>{{ users.length }}</strong></p>
        </div>

        <nav aria-label="...">
          <ul class="pagination justify-content-end">
            <li class="page-item" v-bind:class="{ disabled: configs.currentPage === 1 }">
              <a class="page-link" href="#" aria-label="Previous" @click="previousPage">
                <span aria-hidden="true">&laquo;</span>
                <span class="sr-only">Previous</span>
              </a>
            </li>

            <li class="page-item"
              @click="configs.currentPage = n"
              v-bind:class="{ active: n === configs.currentPage }"
              v-for="n in configs.navPerPage"
              v-bind:key="n">
                <a class="page-link" href="#">{{ n }}</a>
            </li>

            <li class="page-item" v-bind:class="{ disabled: configs.currentPage === configs.navPerPage }">
              <a class="page-link" href="#" aria-label="Next" @click="nextPage">
                <span aria-hidden="true">&raquo;</span>
                <span class="sr-only">Next</span>
              </a>
            </li>
          </ul>
        </nav>

      </div>

    </main>

  </div>
</template>

<script>
import _ from 'lodash'

export default {
  name: 'App',
  data () {
    return {
      users: [],
      configs: {
        perPage: 4,
        filter: '',
        navPerPage: '',
        currentPage: 1
      }
    }
  },
  methods: {
    loadUsers () {
      fetch('static/users.json')
        .then(response => response.json())
        .then(json => {
          this.users = _.orderBy(json, ['name'], ['asc'])
          this.configs.navPerPage = Math.ceil(this.users.length / this.configs.perPage)
        })
    },
    paginate (data, pageSize, pageNumber) {
      --pageNumber
      return this.users.slice(pageNumber * pageSize, (pageNumber + 1) * pageSize)
    },
    previousPage () {
      if (this.configs.currentPage === 1) return false
      this.configs.currentPage -= 1
    },
    nextPage () {
      if (this.configs.currentPage === this.configs.navPerPage) return false
      this.configs.currentPage += 1
    }
  },
  computed: {
    filteredList () {
      if (this.configs.filter) {
        return _.filter(this.users, user => user.name.toLowerCase().includes(this.configs.filter.toLowerCase()))
      }

      return this.paginate(this.users, this.configs.perPage, this.configs.currentPage)
    }
  },
  mounted () {
    this.loadUsers()
  }
}
</script>
