<template>
  <div class="container">
    <div class="row justify-content-center mt-5">
      <div class="col-md-8">
        <h3>
          <img src="../assets/logo.png" width="50" height="50" /> Crud Persons
        </h3>
        <!-- Button trigger modal -->
        <hr />
        <button
          type="button"
          class="btn btn-primary btn-sm"
          data-bs-toggle="modal"
          data-bs-target="#modelId"
          id="btnNew"
          v-on:click="reset()"
        >
          Add
        </button>

        <!-- Modal -->
        <div
          class="modal fade"
          id="modelId"
          tabindex="-1"
          role="dialog"
          aria-labelledby="modelTitleId"
          aria-hidden="true"
        >
          <div class="modal-dialog" role="document">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title">Modal records</h5>
                <button
                  type="button"
                  class="btn-close"
                  data-bs-dismiss="modal"
                  aria-label="Close"
                ></button>
              </div>
              <form
                method="POST"
                v-on:submit.prevent="addData()"
                enctype="multipart/form-data"
                id="formData"
              >
                <div class="modal-body">
                  <input type="hidden" id="id" />
                  <div class="mb-3">
                    <label for="dni" class="form-label">Dni</label>
                    <input
                      type="text"
                      maxlength="10"
                      class="form-control"
                      id="dni"
                      name="dni"
                      required
                    />
                  </div>
                  <div class="mb-3">
                    <label for="names" class="form-label">Names</label>
                    <input
                      type="text"
                      class="form-control"
                      id="names"
                      name="names"
                      required
                    />
                  </div>
                  <div class="mb-3">
                    <label for="exampleInputEmail1" class="form-label"
                      >Email address</label
                    >
                    <input
                      type="email"
                      class="form-control"
                      id="email"
                      name="email"
                      aria-describedby="emailHelp"
                      required
                    />
                  </div>
                  <div class="mb-3">
                    <label for="dni" class="form-label">Photo view</label>
                    <div class="container">
                      <img
                        v-if="url"
                        :src="url"
                        alt=""
                        width="50"
                        id="viewInputImage"
                      />
                    </div>
                  </div>
                  <div class="mb-3">
                    <label for="dni" class="form-label">Photo</label>
                    <input
                      type="file"
                      class="form-control"
                      id="file"
                      name="file"
                      v-on:change="viewImage()"
                    />
                  </div>
                </div>
                <div class="modal-footer">
                  <button
                    type="button"
                    class="btn btn-secondary btn-sm"
                    data-bs-dismiss="modal"
                    id="btnClose"
                  >
                    Close
                  </button>
                  <button type="submit" class="btn btn-primary btn-sm">
                    Save
                  </button>
                </div>
              </form>
            </div>
          </div>
        </div>

        <div class="table-resposive">
          <!-- Table -->
          <table class="table table-striped mt-3">
            <thead id="tablePersons">
              <tr>
                <th>#</th>
                <th>Dni</th>
                <th>Names</th>
                <th>Email</th>
                <th>Photo</th>
                <th>Status</th>
                <th colspan="2">Options</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(list, i) in listPersons" :key="list.id">
                <td scope="row">{{ i + 1 }}</td>
                <td id="dataDni">{{ list.dni }}</td>
                <td>{{ list.names }}</td>
                <td id="dataEmail">{{ list.email }}</td>
                <td>
                  <img
                    :src="src + list.photo"
                    alt="photo"
                    width="50"
                    id="img"
                  />
                </td>
                <td v-if="list.status === 'A'">
                  <span class="badge bg-success">Active</span>
                </td>
                <td v-else>
                  <span class="badge bg-warning">Inactive</span>
                </td>
                <td>
                  <button
                    type="button"
                    class="btn btn-success btn-sm"
                    id="btnEdit"
                    v-on:click="getData(list)"
                  >
                    Edit
                  </button>
                  <button
                    type="button"
                    class="btn btn-danger btn-sm"
                    v-on:click="deleteData(list)"
                  >
                    Delete
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
const url = "http://localhost:7000/";
export default {
  data() {
    return {
      src: url + "public/uploads/",
      listPersons: [],
      url: null,
    };
  },
  mounted() {
    this.listData();
  },
  methods: {
    viewImage() {
      var file = document.querySelector("input[type=file]").files[0];
      this.url = URL.createObjectURL(file);
    },
    soloNumeros(dni) {
      var c = 0;
      for (let index = 0; index < dni.length; index++) {
        if (dni[index] >= 0 && dni[index] <= 9) {
          c = c + 1;
        } else {
          c = c - 1;
        }
      }
      if (c === dni.length) {
        return true;
      } else {
        return false;
      }
    },
    listData() {
      axios
        .get(url + "?option=GET")
        .then((res) => {
          this.listPersons = res.data;
        })
        .catch((err) => {
          console.error(err);
        });
    },
    addData() {
      let id = document.getElementById("id").value;
      var params = new FormData();
      params.append("dni", document.getElementById("dni").value);
      params.append("names", document.getElementById("names").value);
      params.append("email", document.getElementById("email").value);
      params.append(
        "file",
        document.querySelector("input[type=file]").files[0]
      );
      params.append("id", document.getElementById("id").value);
      if (this.soloNumeros(document.getElementById("dni").value)) {
        if (id === "") {
          this.saveData(params);
        } else {
          this.updateData(params);
        }
      } else {
        alert("DNI must only have numbers from 0 - 9");
        document.getElementById("dni").focus();
      }
    },
    saveData(params) {
      axios
        .post(url + "?option=POST", params)
        .then((res) => {
          if (res.data === "DNI is already taken") {
            alert(res.data);
            document.getElementById("dni").focus();
          } else if (res.data === "EMAIL is already taken") {
            alert(res.data);
            document.getElementById("email").focus();
          } else {
            alert("Saved data");
            this.listData();
            document.getElementById("btnClose").click();
          }
        })
        .catch((err) => {
          console.error(err);
        });
    },
    updateData(params) {
      axios
        .post(url + "?option=PUT", params)
        .then((res) => {
          if (res.data === "DNI is already taken") {
            alert(res.data);
            document.getElementById("dni").focus();
          } else if (res.data === "EMAIL is already taken") {
            alert(res.data);
            document.getElementById("email").focus();
          } else {
            alert("Updated data");
            this.listData();
            document.getElementById("btnClose").click();
          }
        })
        .catch((err) => {
          console.error(err);
        });
    },
    getData(data) {
      document.getElementById("btnNew").click();
      document.getElementById("id").value = data.id;
      document.getElementById("dni").value = data.dni;
      document.getElementById("names").value = data.names;
      document.getElementById("email").value = data.email;
      this.url = this.src + data.photo;
    },
    deleteData(data) {
      var params = new FormData();
      params.append("id", data.id);
      if (data.status === "A") {
        if (confirm("Do you want to delete this record?")) {
          axios
            .post(url + "?option=DELETE", params)
            .then(() => {
              alert("Deleted data");
              this.listData();
            })
            .catch((err) => {
              console.error(err);
            });
        }
      } else {
        alert("The state is already inactive");
      }
    },
    reset() {
      this.url = null;
      document.getElementById("id").value = "";
      document.getElementById("formData").reset();
    },
  },
};
</script>

<style>
</style>