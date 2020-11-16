<template>
 <v-main class="listUGD">
   <h3 class="text-h3 font-weight-medium mb-5">To Do List UGD</h3>

   <v-card>
     <v-card-title>
       <v-text-field
         v-model="search"
         append-icon="mdi-magnify"
         label="Search"
         single-line
         hide-details
       ></v-text-field>
       <v-spacer></v-spacer>
       <v-select v-model="sort" label="Urutkan" outlined :items="['Penting','Tidak Penting']" hide-details></v-select>
       <v-spacer></v-spacer>
       <v-btn color="success" dark @click="dialog = true">
         Tambah
       </v-btn>
     </v-card-title>
     <v-data-table :headers="headers" :items="todos" :search="search" :single = "search" :expanded.sync = "expanded" item-key = "note" show-expand class = "elevation-1">

       <template v-slot:[`item.priority`]="{ item }">
          <v-chip v-if="item.priority == 'Penting'" color="red" outlined>
              {{ item.priority }}
          </v-chip>
          <v-chip v-else-if="item.priority == 'Biasa'" color="primary" outlined>
              {{ item.priority }}
          </v-chip>
          <v-chip v-else color="success" outlined>
              {{ item.priority }}
          </v-chip>
                
      </template>
                
      <template v-slot:[`item.actions`]="{ item }">
         <v-btn small class="mr-2" @click="editItem(item)">
          edit
         </v-btn>
         <v-btn small @click="deleteItem(item)">
           delete
         </v-btn>
       </template>
       <template v-slot:[`item.data`]="{ item }">
            <v-checkbox multiple :key="item" @click.capture.stop="pilihData(item)"/>              
       </template>

       <template v-slot:expanded-item="{ headers, item }">
          <td :colspan="headers.length">
            <br>
            <h2 class="text-left"> Note : </h2>
            <br>
            <p class="text-left">{{ item.note }}</p>
            <br>
          </td>
       </template>

     </v-data-table>
   </v-card>

   <v-card v-if="Hapus.length" class="pa-5 mt-5">
        <p>Delete Multiple : </p>
            <ul v-for="del in Hapus" :key="del">
                <li>{{ del.task }}</li>
            </ul>
            <br>
        <v-btn color="red" dark class="mr-2" @click="hapusSemua">HAPUS SEMUA</v-btn>
    </v-card>

   <v-dialog v-model="dialog" persistent max-width="600px">
     <v-card>
       <v-card-title>
         <span class="headline">Form Todo</span>
         </v-card-title>
       <v-card-text>
         <v-container>
           <v-text-field
             v-model="formTodo.task"
             label="Task"
             required
           ></v-text-field>

           <v-select
             v-model="formTodo.priority"
             :items="['Penting', 'Biasa', 'Tidak penting']"
             label="Priority"
             required
           ></v-select>

           <v-textarea
             v-model="formTodo.note"
             label="Note"
             required
           ></v-textarea>
         </v-container>
       </v-card-text>
       <v-card-actions>
         <v-spacer></v-spacer>
         <v-btn color="blue darken-1" text @click="cancel">
           Cancel
         </v-btn>
         <v-btn color="blue darken-1" text @click="save">
           Save
         </v-btn>
       </v-card-actions>
     </v-card>
   </v-dialog>

   <v-dialog v-model="dialogEdit" persistent max-width="600px">
     <v-card>
       <v-card-title>
         <span class="headline">Form Todo EDIT</span>
         </v-card-title>
       <v-card-text>
         <v-container>
           <v-text-field
             v-model="formTodo.task"
             label="Task"
             required
           ></v-text-field>

           <v-select
             v-model="formTodo.priority"
             :items="['Penting', 'Biasa', 'Tidak penting']"
             label="Priority"
             required
           ></v-select>

           <v-textarea
             v-model="formTodo.note"
             label="Note"
             required
           ></v-textarea>
         </v-container>
       </v-card-text>
       <v-card-actions>
         <v-spacer></v-spacer>
         <v-btn color="blue darken-1" text @click="cancel">
           Cancel
         </v-btn>
         <v-btn color="blue darken-1" text @click="edit">
           Edit
         </v-btn>
       </v-card-actions>
     </v-card>
   </v-dialog>

   <v-dialog v-model="dialogHapus" persistent max-width="300px">
     <v-card>
       <v-card-title>
         <span class="headline">Are you sure ?</span>
       </v-card-title>
       <v-card-actions>
         <v-spacer></v-spacer>
         <v-btn color="blue darken-1" text @click="btnBatal">
           Tidak
         </v-btn>
         <v-btn color="red" text @click="btnHapus">
           Iya
         </v-btn>
       </v-card-actions>
     </v-card>
   </v-dialog>
 </v-main>
</template>
<script>
export default {
 name: "List",
 data() {
     return {
     expanded: [],
     singleExpand: false,
     search: null,
     dialog: false,
     dialogHapus: false,
     dialogEdit: false,
     Hapus: [],
     headers: [
       {
         text: "Task",
         align: "start",
         sortable: true,
         value: "task",
       },
       { text: "Priority", value: "priority" },
       { text: "Actions", value: "actions" },
       { text: "", value: "data" },
     ],
     todos: [
       {
         task: "bernafas",
         priority: "Penting",
         note: "huffttt",
       },
       {
         task: "nongkrong",
         priority: "Tidak penting",
         note: "bersama tman2",
       },
       {
         task: "masak",
         priority: "Biasa",
         note: "masak air 500ml",
       },
     ],
     formTodo: {
       task: null,
       priority: null,
       note: null,
     },
   };
 },
 methods: {
   save() {
     this.todos.push(this.formTodo);
     this.resetForm();
     this.dialog = false;
   },
   cancel() {
     this.resetForm();
     this.dialog = false;
     this.dialogEdit=false;
   },
   edit(){
      this.save();
      this.todos = this.todos.filter(todo => todo !== this.temp);
      this.temp = null;
      this.dialogEdit = false;
   },
   resetForm() {
     this.formTodo = {
       task: null,
       priority: null,
       note: null,
     };
   },
   editItem(item){
       this.dialogEdit = true;
       this.formTodo = {
        task: item.task,
        priority: item.priority,
        note: item.note,
       };
       this.temp = item;
   },

   deleteItem(item){
     this.dialogHapus = true;
     this.delete = this.todos.indexOf(item);
   },

   btnBatal(){
      this.dialogHapus =false;
   },

   btnHapus(){
     this.todos.splice(this.delete, 1);
     this.dialogHapus = false;
   },
   
   pilihData(item) {
     this.Hapus.includes(item)  ?
     this.Hapus.splice(this.Hapus.indexOf(item), 1) :
     this.Hapus.push(item);                
   },
   
   hapusSemua() {
     for (var i=0; i < this.Hapus.length; i++) {
        if (this.formTodo.task == this.Hapus[i].task) {
            this.formTodo = {
             task: null,
             priority: null,
             note: null,
            };
        }
        this.todos.splice(this.todos.indexOf(this.Hapus[i]), 1);
    }
    this.Hapus = [];
    },
 },
};
</script>