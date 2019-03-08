<template>
<div>

  <v-card-title>
    <v-btn color="primary" @click="addBrand">新增品牌</v-btn>
    <!--搜索框，与search属性关联-->
    <v-spacer/>
    <v-flex xs3>
      <v-text-field label="搜索" v-model="key" append-icon="search" hide-details/>
    </v-flex>
  </v-card-title>
  <v-data-table
    :headers="headers"
    :items="brands"
    :pagination.sync="pagination"
    :total-items="totalBrands"
    :loading="loading"
    class="elevation-1"
  >
    <template slot="items" slot-scope="props">
      <td class="text-xs-center">{{ props.item.id }}</td>
      <td class="text-xs-center">{{ props.item.name }}</td>
      <td class="text-xs-center">
        <img v-if="props.item.image" :src="props.item.image" width="130" height="40">
        <span v-else>无</span>
      </td>
      <td class="text-xs-center">{{ props.item.letter }}</td>
      <td class="text-xs-left">
        <v-btn flat icon color="info">
          <v-icon>edit</v-icon>
        </v-btn>
        <v-btn flat icon color="error">
          <v-icon>delete</v-icon>
        </v-btn>
      </td>
    </template>
  </v-data-table>

  <v-dialog max-width="500" v-model="show" persistent>
    <v-card>
      <!--对话框的标题-->
      <v-toolbar dense dark color="primary">
        <v-toolbar-title>新增品牌</v-toolbar-title>
        <v-spacer/>
        <v-btn icon @click="closeWindow"><v-icon>close</v-icon></v-btn>
      </v-toolbar>
      <!--对话框的内容，表单-->
      <v-card-text class="px-5">
      <my-brand-form></my-brand-form>
      </v-card-text>
    </v-card>
  </v-dialog>
</div>
</template>

<script>
  import MyBrandForm from './MyBrandForm'
    export default {
        name: "MyBrand",
      components:{
          MyBrandForm
      },
      data(){
          return{
            headers: [
              {text: 'id', align: 'center', value: 'id'},
              {text: '名称', align: 'center', sortable: false, value: 'name'},
              {text: 'LOGO', align: 'center', sortable: false, value: 'image'},
              {text: '首字母', align: 'center', value: 'letter', sortable: true,}
            ],
            brands:[],
            pagination:{},
            totalBrands:0,
            loading:false,
            key:"",
            show:false


          }
      },
      watch:{
          key(){
            this.pagination.page=1;
            this.loadBrands();
          },
          pagination:{
            deep:true,
            handler(){
              this.loadBrands();

            }
          }
      },
      methods:{
        loadBrands(){
          this.loading=true;
          this.$http.get("/item/brand/page",{
            params:{
              key:this.key,
              page: this.pagination.page,
              rows: this.pagination.rowsPerPage,
              sortBy: this.pagination.sortBy,
              desc: this.pagination.descending
            }
          }).then(resp=>{

            this.brands = resp.data.items;
            this.totalBrands = resp.data.total;
            // 完成赋值后，把加载状态赋值为false
            this.loading = false;
          })


        },
        addBrand(){
          this.show=true
        },
        closeWindow(){
          this.show=false
        }

      },
      created(){
        this.loadBrands();
      }
    }
</script>

<style scoped>

</style>
