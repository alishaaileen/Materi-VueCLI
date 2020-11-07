<!-- @format -->

<template>
  <v-main class="list-UGD">
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
        <v-btn color="purple mr-3" dark @click="dialogRecycleBin.state = true">
          Todo Selesai
        </v-btn>
        <v-btn color="success" dark @click="dialog = true">
          Tambah
        </v-btn>
      </v-card-title>
      <v-data-table :headers="headers" :items="todos" :search="search">
        <template v-slot:[`item.priority`]="{ item }">
          <v-chip :color="getColorPriority(item.priority)" label outlined>
            {{ item.priority }}
          </v-chip>
        </template>
        <template v-slot:[`item.actions`]="{ item }">
          <v-icon color="info" class="mr-2" @click="setFormEdit(item)">
            mdi-pencil
          </v-icon>
          <v-icon color="error" @click="deleteItemConfirm(item)">
            mdi-delete
          </v-icon>
        </template>
      </v-data-table>
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
          <v-btn color="blue darken-1" text @click="save(idEdit)">
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogDelete" persistent max-width="350">
      <v-card>
        <v-card-title class="headline">
          Yakin ingin menghapus?
        </v-card-title>
        <v-card-text></v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" text @click="cancelDelete">
            Tidak
          </v-btn>
          <v-btn color="red darken-1" text @click="deleteItem()">
            Ya
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogRecycleBin.state" max-width="800">
      <v-card>
        <v-card-title>
          <span class="headline">Finished To do</span>
        </v-card-title>
        <v-data-table :headers="dialogRecycleBin.headers" :items="dialogRecycleBin.todos">
        <template v-slot:[`item.priority`]="{ item }">
          <v-chip :color="getColorPriority(item.priority)" label outlined>
            {{ item.priority }}
          </v-chip>
        </template>
      </v-data-table>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="info" text @click="dialogRecycleBin.state = false">
          Close
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
      search: null,
      dialog: false,
      dialogDelete: false,
      headers: [
        {
          text: "Task",
          align: "start",
          sortable: true,
          value: "task",
        },
        { text: "Priority", value: "priority" },
        { text: "Note", value: "note" },
        { text: "Actions", value: "actions" },
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
      dialogRecycleBin: {
        state: false,
        headers: [
          { text: "Task", align: "start", sortable: true, value: "task" },
          { text: "Priority", value: "priority" },
          { text: "Note", value: "note" },
        ],
        todos: []
      }
    };
  },
  methods: {
    getColorPriority(priority) {
      if (priority === "Penting") return "error";
      else if (priority === "Biasa") return "info";
      else return "success";
    },
    save(idEdit = null) {
      idEdit == null ? this.add() : this.edit(idEdit);
      this.resetForm();
      this.dialog = false;
    },
    cancel() {
      this.resetForm();
      this.dialog = false;
    },
    setFormEdit(item) {
      this.idEdit = this.todos.indexOf(item);

      this.formTodo.task = item.task;
      this.formTodo.priority = item.priority;
      this.formTodo.note = item.note;

      this.dialog = true;
    },
    resetForm() {
      this.formTodo = {
        task: null,
        priority: null,
        note: null,
      };
      this.idEdit = null;
    },
    // CREATE ===========================================
    add() {
      this.todos.push(this.formTodo);
    },
    // EDIT ===========================================
    edit(idEdit) {
      this.todos[idEdit].task = this.formTodo.task;
      this.todos[idEdit].priority = this.formTodo.priority;
      this.todos[idEdit].note = this.formTodo.note;
      console.log(this.todos[idEdit]);
    },
    // DELETE ===========================================
    deleteItemConfirm(item) {
      this.idEdit = this.todos.indexOf(item);
      this.dialogDelete = true;
    },
    cancelDelete() {
      this.resetForm();
      this.dialogDelete = false;
    },
    deleteItem() {
      this.dialogRecycleBin.todos.push(this.todos[this.idEdit])
      this.todos.splice(this.idEdit, 1);
      this.resetForm();
      this.dialogDelete = false;
    },
  },
};
</script>
