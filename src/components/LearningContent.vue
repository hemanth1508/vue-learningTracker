<template>
  <v-data-table
    :headers="headers"
    :items="desserts"
    sort-by="date"
    class="elevation-1 theme--light"
  >
    <template v-slot:item.topic="props">
      <a :href="`${props.item.link}`" target="_blank">
        {{ props.item.topic }}
      </a>
    </template>
    <template v-slot:top>
      <v-toolbar flat color="white">
        <v-toolbar-title>My Learning</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on }">
            <v-btn color="primary" dark class="mb-2" v-on="on"
              >New Learning</v-btn
            >
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12">
                    <v-overflow-btn
                      v-model="editedItem.category"
                      class="my-2"
                      label="Category"
                      :items="dropdown_font"
                    ></v-overflow-btn>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12">
                    <v-text-field
                      v-model="editedItem.topic"
                      label="Topic"
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12">
                    <v-text-field
                      v-model="editedItem.link"
                      label="Link"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="save">Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.action="{ item }">
      <v-icon small class="mr-2" color="blue darken-1" @click="editItem(item)">
        edit
      </v-icon>
      <v-icon small color="red darken-1" @click="deleteItem(item)">
        delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">Reset</v-btn>
    </template>
  </v-data-table>
</template>
<script>
import axios from "axios";
export default {
  data: () => ({
    dialog: false,
    headers: [
      {
        text: "Date",
        align: "left",
        sortable: true,
        value: "date"
      },
      { text: "Topic", value: "topic" },
      { text: "Category", value: "category" },

      { text: "Actions", value: "action", sortable: false }
    ],
    desserts: [],
    dropdown_font: ["Web", "Cloud", "Mobile", "Others"],
    editedIndex: -1,
    editedItem: {
      date: "",
      topic: "",
      link: "",
      category: ""
    },
    defaultItem: {
      date: "",
      topic: "",
      link: "",
      category: ""
    }
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Item" : "Edit Item";
    }
  },

  watch: {
    dialog(val) {
      val || this.close();
    }
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {
      let here = this;
      axios
        .get("https://c2iioe0ahf.execute-api.ap-south-1.amazonaws.com/dev/")
        .then(function(response) {
          here.desserts = response.data.Items;
        });
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      const index = this.desserts.indexOf(item);
      confirm("Are you sure you want to delete this item?") &&
        this.desserts.splice(index, 1);
    },

    close() {
      this.dialog = false;
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      }, 300);
    },

    setDate() {
      var today = new Date();
      const monthNames = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December"
      ];

      var dd = String(today.getDate()).padStart(2, "0");
      var mm = String(monthNames[today.getMonth()]);
      var yyyy = today.getFullYear();

      today = dd + "-" + mm + "-" + yyyy;
      this.editedItem.date = today;
    },

    save() {
      this.setDate();
      let here = this;
      axios
        .post(
          "https://c2iioe0ahf.execute-api.ap-south-1.amazonaws.com/dev/",
          this.editedItem
        )
        .then(function(response) {
          if (response.data.status == "success") {
            if (here.editedIndex > -1) {
              Object.assign(here.desserts[here.editedIndex], here.editedItem);
            } else {
              here.desserts.push(here.editedItem);
            }
          } else {
            alert("ERROR: Record not created.");
          }
        })
        .catch(function(error) {
          alert(error);
        });

      this.close();
    }
  }
};
</script>
