<template>
    <div>
        <h1 class="text-center">
            Gestionar Artículos
        </h1>
        <hr>
        <!-- Button to Open the Modal -->
        <button type="button" class="btn btn-primary mb-5" @click="modificar=false;abrirModal()">
        Nuevo
        </button>

        <!-- The Modal -->
        <div class="modal" :class="{mostrar:modal}" >
        <div class="modal-dialog">
            <div class="modal-content">

            <!-- Modal Header -->
            <div class="modal-header">
                <h4 class="modal-title">{{ tituloModal }}</h4>
                <button @click="cerrarModal()" type="button" class="close" data-dismiss="modal">&times;</button>
            </div>

            <!-- Modal body -->
            <div class="modal-body">

                <div class="form-group">
                    <label for="nobre">Nombre</label>
                    <input type="text" class="form-control" id="nombre" placeholder="Nombre del articulo" v-model="articulo.nombre">
                </div>
                
                <div class="form-group">
                    <label for="nobre">Descripción</label>
                    <textarea  class="form-control" id="descripcion" v-model="articulo.descripcion">Descripción</textarea>
                </div> 

                <div class="form-group">
                    <label for="nobre">Stock</label>
                    <input type="number"  class="form-control" id="stock" value="0" v-model="articulo.stock">
                </div>   
            </div>

            <!-- Modal footer -->
            <div class="modal-footer">
                <button type="button" @click="cerrarModal()" class="btn btn-secondary" data-dismiss="modal">Close</button>
                <button type="button" @click="guardar()" class="btn btn-success" data-dismiss="modal">Guardar</button>
            </div>

            </div>
        </div>
        </div>
        <table class="table table-striped">
        <thead class="thead-dark">
            <tr>
            <th scope="col">#</th>
            <th scope="col">Nombre</th>
            <th scope="col">Descripción</th>
            <th scope="col">Stock</th>
            <th scope="col" colspan="2" class="text-center">Accion</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="art in articulos" :key="art.id">
                <th scope="row">{{art.id}}</th>
                <td>{{ art.nombre }}</td>
                <td>{{ art.descripcion }}</td>
                <td>{{ art.stock }}</td>
                <td>
                    <button  @click="modificar=true;abrirModal(art)" class="btn btn-warning" >Editar</button>
                </td>
                 <td>
                    <button @click="eliminar(art.id)" class="btn btn-danger">Eliminar</button>
                </td>
            </tr>
        </tbody>
        </table>
    </div>
</template>

<script>
export default {
    data(){
        return{
            articulo :{
                        'nombre':'',
                        'descripcion':'',
                        'stock':''
            },
            id : 0,
            modificar: true,
            modal:0,
            tituloModal:'',
            articulos : [],
        }
    },
    methods:{
        //Para consultar todos los registros
        async listar(){
            let res= await axios.get('articulos');
            this.articulos = res.data;
        },
        //Para eliminar
        async eliminar(id){
            let res= await axios.delete('articulos/'+id);
            if(res){
                this.listar();
            }
        },
        async guardar(){
            if(this.modificar){
                let res= await axios.put('articulos/'+this.id,this.articulo);
            }else{
                let res= await axios.post('articulos', this.articulo);
            }
            this.cerrarModal();
            this.listar();
        },
        abrirModal(data={}){
            this.modal = 1;
            if(this.modificar){
                this.id = data.id;
                this.tituloModal= "Modificar Artículo";
                this.articulo.nombre = data.nombre;
                this.articulo.descripcion = data.descripcion;
                this.articulo.stock = data.stock;
            }else{
                this.tituloModal= "Guardar Artículo";
                this.articulo.nombre = '';
                this.articulo.descripcion = '';
                this.articulo.stock = 1;
            }
        },
        cerrarModal(){
            this.modal = 0;
        }
    },
    created(){
        this.listar();
    }
}
</script>

<style>
.mostrar{
    display: list-item;
    opacity: 1;
    background: rgba(44,38,75,0.849);
}
</style>