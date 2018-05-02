<template>
<div>
<nav class="panel column is-offset-2 is-8">
  <p class="panel-heading">
    VueJS Phonebook 
   <button class="button is-link is-outlined" @click="openAdd">
     Add New
    </button>
    <div class="is-pulled-right" v-if="loading">
      <i class="fa fa-refresh" aria-hidden="true"></i>
    </div>
  </p>
  <div class="panel-block">
    <p class="control has-icons-left">
      <input class="input is-small" type="text" placeholder="search" v-model="searchQuery">
      <span class="icon is-small is-left">
        <i class="fas fa-search" aria-hidden="true"></i>
      </span>
    </p>
  </div>

  <a class="panel-block" v-for="item,key in tmp">
 <div class="is-9 column">
   {{key}}. {{item.name}} 

 </div>

     <span class="panel-icon column is-1"><i class="has-text-danger fa fa-trash" aria-hidden="true" @click="del(key,item.id)">D</i></span>
     <span class="panel-icon column is-1"><i class="has-text-info fa fa-edit" aria-hidden="true" @click="openEdit(key)">E</i></span>
     <span class="panel-icon column is-1"><i class="has-text-primary fa fa-eye" aria-hidden="true" @click="openShow(key)">V</i></span>


  </a>
</nav>

	<Add :openmodal="addActive" @closeRequest="close"></Add>
  <Show :openmodal="showModal" @closeRequest="close"></Show>
  <Edit :openmodal ="editModal" @closeRequest="close"></Edit>

  </div>
</template>
<script>
let Add = require("./Add.vue"),
  Show = require("./Show.vue"),
  Edit = require("./Edit.vue");

export default {
  components: { Add, Show, Edit },
  data() {
    return {
      addActive: "",
      showModal: "",
      editModal: "",
      loading: false,
      searchQuery: "",
      lists: {},
      errors: {},
      tmp: ""
    };
  },
  watch: {
    searchQuery() {
      if (this.searchQuery.length > 0) {
        this.tmp = this.lists.filter(item => {
          return Object.keys(item).some(key => {
            let string = String(item[key]);

            return (
              string.toLowerCase().indexOf(this.searchQuery.toLowerCase()) > -1
            );
          });
        });
      } else {
        this.tmp = this.lists;
      }
    }
  },
  mounted() {
    axios
      .post("/getData")
      .then(response => (this.lists = this.tmp = response.data))
      .catch(error => (this.errors = error.response.data.errors));
  },
  methods: {
    openAdd() {
      this.addActive = "is-active";
    },
    close() {
      this.addActive = this.showModal = this.editModal = "";
    },
    openShow(key) {
      this.showModal = "is-active";
      this.$children[1].list = this.tmp[key];
    },
    openEdit(key) {
      this.editModal = "is-active";
      this.$children[2].list = this.tmp[key];
    },
    del(key, id) {
      if (confirm("Are your sure to delete ?")) {
        this.loading = !this.loading;
        axios
          .delete(`/phonebook/${id}`)
          .then(response => {
            this.loading = !this.loading;
            this.lists.splice(key, 1);
          })
          .catch(error => (this.errors = error.response.data.errors));
      }
    }
  }
};
</script>