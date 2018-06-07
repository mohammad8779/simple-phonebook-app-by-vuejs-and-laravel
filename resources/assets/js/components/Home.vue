<template>

<div>
	<nav class="panel column is-offset-2 is-8">
  <p class="panel-heading">
    VueJs Phonebook
  </p>

  <div class="panel-block">
    <button class="button is-link is-outlined" @click="openAdd">
      Add New
    </button>
    <span class="is-pulled-right" v-if="loading" >
       <i class="fa fa-refresh fa-spin fa-2x fa-fw"></i>
    </span>
  </div>

  <div class="panel-block">
    <p class="control has-icons-left">
      <input class="input is-small" type="text" v-model="searchQuery" placeholder="search">
      <span class="icon is-small is-left">
        <i class="fas fa-search" aria-hidden="true"></i>
      </span>
    </p>
  </div>
  
  
  <a class="panel-block" v-for="item,key in temp">
	
	<span class="column is-9">
     	{{ item.name }}
    </span>
  
    <span class="panel-icon column is-1">
     <i class="has-text-danger fa fa-trash" @click="del(key,item.id)" ></i>
    </span>
    <span class="panel-icon column is-1">
      <i class="has-text-info fa fa-edit" @click="openUpdate(key)"></i>
    </span>

    <span class="panel-icon column is-1">
      <i class="has-text-primary fa fa-eye" @click="openShow(key)"></i>
    </span>
    
  </a>
 </nav>
  <Add :openmodal='addActive' @closeRequest='close'></Add>
  <Show :openmodal='showActive' @closeRequest='close'></Show>
  <Update :openmodal='showUpdate' @closeRequest='close'></Update>
</div>	
</template>
<script>
	let Add =  require('./Add.vue');
  let Show =  require('./Show.vue');
  let Update =  require('./Update.vue');
	export default{
		components:{Add,Show,Update},

		data(){
			return{
				addActive:'',
        showActive:'',
        showUpdate:'',
        lists:{},
        errors:{},
        loading:false,
        searchQuery:'',
        temp:''
        }
    },

    watch:{
      searchQuery(){
          if(this.searchQuery.length > 0){
              this.temp = this.lists.filter((item) => {
               return Object.keys(item).some((key) =>{
                  let string = String(item[key])
                   return string.toLowerCase().indexOf(this.searchQuery.toLowerCase())>-1
                  //console.log(string)
                })
              
              });
             // console.log(result)
          }else{
             this.temp = this.lists
          }
      }
    },
   
    mounted(){
         axios.post('/getData')
         .then((response) => this.lists = this.temp =  response.data )
         .catch( (error) => this.errors = error.response.data.errors );
    },
		methods:{
			openAdd(){
				this.addActive = 'is-active';
			},
      openShow(key){
        this.$children[1].list = this.temp[key]
        this.showActive = 'is-active';
      },
       openUpdate(key){
        this.$children[2].list = this.temp[key]
        this.showUpdate = 'is-active';
      },
			close(){
				this.addActive = this.showActive = this.showUpdate = ''
			},
      del(key,id){
          if(confirm("Are You Sure?")){
               axios.delete(`/phonebook/${id}`)
              .then((response) => {this.lists.splice(key,1);this.loading = !this.loading})
              .catch( (error) => this.errors = error.response.data.errors );
            }
          console.log(`${key} ${id}`)
      }
		}
	}
</script>