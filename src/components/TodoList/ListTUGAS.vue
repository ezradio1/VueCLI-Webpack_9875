<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>

    <div class="d-flex align-start">
        <v-card
        class="mr-5">
            <v-card-title primary-title>
                <v-text-field
                v-model="search"
                append-icon="mdi-magnify"
                label="search"
                single-line
                hide-details></v-text-field>
                <v-spacer></v-spacer>
                    <v-btn color="success" dark @click="dialog=true;">
                    Tambah
                </v-btn>
             
            </v-card-title>
           
            <v-data-table 
                :headers="headers"
                :items="todos"
                :search="search"
                >
                
            <template v-slot:[`item.priority`]="{ item }">
                <td>
                <v-chip
                    v-if="item.priority == 'Penting'"
                    class="ma-2"
                    color="red"
                    label
                    outlined
                    >
                    {{ item.priority }}
                </v-chip>
                <v-chip
                    v-else-if="item.priority == 'Biasa'"
                    class="ma-2"
                    color="blue"
                    label
                    outlined
                    >
                    {{ item.priority }}
                </v-chip>
                <v-chip
                    v-else-if="item.priority == 'Tidak Penting'"
                    class="ma-2"
                    color="green"
                    label
                    outlined
                    >
                    {{ item.priority }}
                </v-chip>
                </td>

            </template>
             <template v-slot:[`item.checks`]="{ item }">
                    <v-checkbox multiple :key="item" @click.capture.stop="cekList(item)"/>              
                </template>

            <template v-slot:[`item.actions`]="{ item }">
                 <v-icon
                    small
                    class="mr-2"
                    @click="showData(item)"
                    color="blue"
                >
                    mdi-file
                </v-icon>
                </template>

        </v-data-table>

        </v-card>
        
        
            
        <div
            v-if="detailData == true"
            class="flex">

            <h1>{{ editedItem.task }} <span>    
                
             <v-chip
                    text-color="white"
                    v-if="editedItem.priority == 'Penting'"
                    class="ma-2"
                    color="red"
                    label
                    >
                    {{ editedItem.priority }}
                </v-chip>
                <v-chip
                    text-color="white"
                    v-else-if="editedItem.priority == 'Biasa'"
                    class="ma-2"
                    color="blue"
                    label
                    >
                    {{ editedItem.priority }}
                </v-chip>
                <v-chip
                    text-color="white"
                    v-else-if="editedItem.priority == 'Tidak Penting'"
                    class="ma-2"
                    color="green"
                    label
                    >
                    {{ editedItem.priority }}
                </v-chip>
                </span></h1>
                <p>{{ editedItem.note }}</p>
                <v-icon
                    small
                    class="mr-2"
                    @click="editItem"
                    color="blue"
                >
                    mdi-pencil
                </v-icon>
                <v-icon
                    small
                    @click="deleteItem"
                    color="red"
                >
                    mdi-delete
                </v-icon>
        </div>
        </div>

        <v-card v-if="check.length" class="pa-5 my-2">
            Delete Multiple
                <br>
                <v-list-item v-for="(item, i) in check" :key="i">
                    <v-list-item-content>
                        <v-list-item-title>•  {{item.task}}</v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
                <v-btn color="error" dark @click="deleteAll()">
                    Hapus Semua
            </v-btn>
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
            <v-btn color="blue darken-1" text @click="cancelAdd"> Cancel </v-btn>
            <v-btn color="blue darken-1" text @click="saveAdd"> Save </v-btn>
            </v-card-actions>
        </v-card>
        </v-dialog>

        <v-dialog
                v-model="dialog_edit"
                persistent
                max-width="600px"
                transition="dialog-transition">
            <v-card>
                <v-card-title>
                <span class="headline">From Todo</span>
                </v-card-title>
                <v-card-text>
                <v-container>
                    <v-text-field
                        v-model="tempArray.task"
                        label="Task"
                        required
                    ></v-text-field>
                    <v-select
                        :items="['Penting', 'Biasa', 'Tidak Penting']"
                        v-model="tempArray.priority"
                        label="All Priority"
                        required
                    ></v-select>
                    <v-textarea
                        v-model="tempArray.note" 
                        label="Note" 
                        required
                    ></v-textarea>
                </v-container>
                </v-card-text>
                <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="cancelEdit">Cancel</v-btn>
                <v-btn color="blue darken-1" text @click="saveEdit">Save</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>


        <v-dialog 
            v-model="dialog_delete" 
            persistent 
            max-width="400px">
            <v-card>
                <v-card-title>
                <span class="headline"><b>Yakin ingin menghapus?</b></span>
                </v-card-title>
                <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="green darken-1" text @click="tidak">
                    TIDAK
                </v-btn>
                <v-btn color="red darken-1" text @click="konfirmasiDel">
                    YA
                </v-btn>
                </v-card-actions>
            </v-card>
            </v-dialog>

        </v-main>
</template>
<script>
export default {
    name:"List",
    data(){
        return {
            search:null,
            dialog:false,
            dialog_edit:false,
            dialog_delete:false,
            detailData:false,
            itemContent: [],
            check:[],
            filters:"All Priority",
            headers: [
                {
                    text:'Task',
                    align: 'start',
                    sortable: true,
                    value: 'task',
                },
                { text: "Priority", value:"priority"},
                { text: "Note", value:"note"},
                { text: "Actions", value:"actions"},
                { text: "", value:"checks"},
            ],
            todos: [
                {
                    task: "bernafas",
                    priority: "Penting",
                    note: "hufftts",
                },
                {
                    task: "nongkrong",
                    priority: "Tidak Penting",
                    note: "bersama teman teman"
                },
                {
                    task: "mainpubg",
                    priority: "Penting",
                    note: "bersama dia"
                },
            ],
            formTodo:  {
                task: null,
                priority: null,
                note: null,
            },
            editedItem: {
                task: null,
                priority: null,
                note: null,
            },
            tempArray: {
                task: null,
                priority: null,
                note: null,
            },
            noteArr: {
                task: null,
                priority: null,
                note: null,
            },
        };
    },
    methods: {
        saveEdit(){
            if (this.editedIndex > -1) {
            Object.assign(this.todos[this.editedIndex], this.tempArray)
            } else {
            this.todos.push(this.tempArray)
            }
            this.dialog_edit = false;
            this.detailData = false;
        },
        cancelEdit(){
            this.resetForm();
            this.dialog_edit = false;
        },

        saveAdd(){
            this.todos.push(this.formTodo);
            this.resetForm();
            this.dialog = false;
        },
        cancelAdd(){
            this.resetForm();
            this.dialog = false;
        },
        tidak(){
            this.resetForm();
            this.dialog_delete = false;
        },
        resetForm(){
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        
        
        editItem(){
            this.editedItem = Object.assign({}, this.todos[this.editedIndex])
            this.tempArray = Object.assign({}, this.todos[this.editedIndex])
            this.dialog_edit = true  
        },
        
        deleteItem() {
            this.dialog_delete = true
        },
        deleteAll(){             
            for(var i = 0; i < this.check.length; i++){       
                if(this.noteArr.task == this.check[i].task && this.noteArr.priority == this.check[i].priority && this.noteArr.note == this.check[i].note)
                {
                    this.detailData=false
                }
            }
            for(var i = 0; i < this.check.length; i++){            
                this.todos.splice( this.todos.indexOf(this.check[i]), 1);
            }
            this.check=[];                          
        },

        konfirmasiDel(){
            this.todos.splice(this.editedIndex, 1);
            this.dialog_delete = false;
            this.detailData = false
        },
        showData(i){
            this.noteArr = []
            this.editedIndex = this.todos.indexOf(i)
            this.noteArr = Object.assign({}, i)
            this.editedItem = Object.assign({}, i)
            this.detailData=true
        },
        cekList(item){
            if(this.check.includes(item)) {
                this.check.splice(this.check.indexOf(item), 1);
            } else {
                this.check.push(item);                
            }
        },
        },
    
};
</script>