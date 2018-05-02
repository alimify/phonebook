<template>
<div>
<nav class="panel column is-offset-2 is-8">
  <p class="panel-heading">
    VueJS Phonebook 
   <button class="button is-link is-outlined" @click="openAdd">
     Add New
    </button>
  </p>
  <div class="panel-block">
    <p class="control has-icons-left">
      <input class="input is-small" type="text" placeholder="search">
      <span class="icon is-small is-left">
        <i class="fas fa-search" aria-hidden="true"></i>
      </span>
    </p>
  </div>

  <a class="panel-block" v-for="item,key in lists">
 <div class="is-9 column">
   {{key}}. {{item.name}} 

 </div>

     <span class="panel-icon column is-1"><i class="has-text-danger fa fa-trash" aria-hidden="true">D</i></span>
     <span class="panel-icon column is-1"><i class="has-text-info fa fa-edit" aria-hidden="true">E</i></span>
     <span class="panel-icon column is-1"><i class="has-text-primary fa fa-eye" aria-hidden="true" @click="openShow(key)">V</i></span>


  </a>
</nav>

	<Add :openmodal="addActive" @closeRequest="close"></Add>
  <Show :openmodal="showModal" @closeRequest="close"></Show>

  </div>
</template>
<script>
let Add = require('./Add.vue'),
    Show =  require('./Show.vue')

export default{
  components: {Add,Show},
  data(){
   return {
     addActive : '',
     showModal: '',
     lists:{},
     errors:{}
   }
  },
  mounted(){
  this.loadData()
  },
  methods: {
    openAdd(){
      this.addActive = 'is-active'
    },
    close(){
      this.addActive = this.showModal = ''
      this.loadData()
    },
    openShow(key){
      this.showModal = 'is-active'
     this.$children[1].list = this.lists[key]
    },
    loadData(){
      axios.post('/getData')
  .then( response => this.lists = response.data )
  .catch( error => this.errors = error.response.data.errors)
    }
  }
}

</script>