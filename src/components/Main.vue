<template>
  <div class="main">
    <div class="drag-container" v-drag-and-drop:options="options">
      <ul class="drag-list">
        <li class="drag-column" v-for="group in groups" :key="group.id">
          <span class="drag-column-header">
            <h1>
              <font-awesome-icon v-if="group.id == 1" :icon="['fas', 'list-ul']" />
              <font-awesome-icon v-if="group.id == 2" :icon="['fas', 'spinner']" />
              <font-awesome-icon v-if="group.id == 3" :icon="['fas', 'check-circle']" />
              {{ group.name }}
            </h1>
            <feather-icon type="more-vertical"></feather-icon>
          </span>
          <vue-draggable-group
            v-model="group.tasks"
            :groups="groups"
            :data-id="group.id"
            @change="onDrop"
          >
            <ul class="drag-inner-list" :data-id="group.id">
              <li class="drag-item" v-for="item in group.tasks" :key="item.id" :data-id="item.id">
                <b-badge
                  class="badge float-right"
                  v-if="group.id == 3"
                  variant="success"
                >Complated on: {{item.cdate}}</b-badge>
                <div class="drag-item-name">{{ item.title }}</div>
                <div class="drag-item-desc">{{ item.desc }}</div>
                <div class="drag-item-date">Due date: {{ item.date }}</div>
              </li>
            </ul>
          </vue-draggable-group>
        </li>
      </ul>
    </div>
    <div class="button-plus" @click="modalShow = !modalShow">
      <h1>
        <font-awesome-icon :icon="['fas', 'plus-circle']" />
      </h1>
    </div>

    <div class="light-box">
      <b-modal
        id="modal-prevent-closing"
        ref="modal"
        title="Create A Task"
        v-model="modalShow"
        @show="resetModal"
        @hidden="resetModal"
        @ok="handleOk"
      >
        <form ref="form" @submit.stop.prevent="handleSubmit">
          <b-form-group
            id="title-group"
            label-for="title-input"
            invalid-feedback="Tite is required"
          >
            <label for="title">TITLE:</label>
            <b-form-input id="title" placeholder="Task Title" v-model="title" required></b-form-input>
          </b-form-group>
          <b-form-group
            id="desc-group"
            label-for="desc-input"
            invalid-feedback="Description is required"
          >
            <label for="desc">DESCRIPTION:</label>
            <b-form-textarea id="desc" placeholder="Describe the task" v-model="desc" required></b-form-textarea>
          </b-form-group>
          <b-form-group id="date-group" label-for="date-input" invalid-feedback="Date is required">
            <label for="date">DUE DATE:</label>
            <date-picker
              id="date"
              v-model="date"
              :config="{format: 'DD/MM/YYYY'}"
              placeholder="dd/mm/yyyy"
            ></date-picker>
          </b-form-group>
        </form>
      </b-modal>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import datePicker from "vue-bootstrap-datetimepicker";
import "pc-bootstrap4-datetimepicker/build/css/bootstrap-datetimepicker.css";
const qs = require("querystring");
export default {
  data() {
    return {
      modalShow: false,
      title: "",
      desc: "",
      date: "",
      task_category_id: "",
      id: "",
      item: [],
      groups: [],
      options: {
        dropzoneSelector: ".drag-inner-list",
        draggableSelector: ".drag-item",
        onDrop: function(id) {
          /* eslint-disable no-console */
          console.log(id.items[0].dataset.id);
          var config = {
            headers: { "Access-Control-Allow-Origin": "*" }
          };
          axios
            .delete(
              "http://localhost:8080/tasks/" + id.items[0].dataset.id,
              config
            )
            .catch(function(error) {
              /* eslint-disable no-console */
              console.log(error);
            }); // assuming your response payload is in a data object
        }
      }
    };
  },
  mounted() {
    axios.get("http://localhost:8080/api/task").then(response => {
      this.groups = response.data;
    });
  },
  name: "Main",
  components: {
    datePicker
  },
  methods: {
    resetModal() {
      this.name = "";
      this.nameState = null;
    },
    handleOk(bvModalEvt) {
      // Prevent modal from closing
      bvModalEvt.preventDefault();
      // Trigger submit handler
      this.handleSubmit();
    },
    handleSubmit() {
      const requestBody = {
        title: document.getElementById("title").value,
        desc: document.getElementById("desc").value,
        date: document.getElementById("date").value,
        taskCategory: 1
      };
      const config = {
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        }
      };
      axios
        .post(
          "http://localhost:8080/api/task",
          qs.stringify(requestBody),
          config
        )
        .catch(function(error) {
          /* eslint-disable no-console */
          console.log(error);
        });
      // Hide the modal manually
      this.$nextTick(() => {
        this.$refs.modal.hide();
      });
    },
    onDragend: function(id) {
      axios
        .delete("http://localhost:8080/api/task/" + id)
        .catch(function(error) {
          /* eslint-disable no-console */
          console.log(error);
        }); // assuming your response payload is in a data object
    },
    onDrop: function(id) {
      axios
        .delete("http://localhost:8080/api/task/" + id)
        .catch(function(error) {
          /* eslint-disable no-console */
          console.log(error);
        }); // assuming your response payload is in a data object
    },
    onGroupsChange: function(id) {
      axios
        .delete("http://localhost:8080/api/task/" + id)
        .catch(function(error) {
          /* eslint-disable no-console */
          console.log(error);
        }); // assuming your response payload is in a data object
    },
    APICall: function() {
      axios.get("http://localhost:8080/api/task").then(response => {
        this.groups = response.data;
      });
    },
    submitProduct: function() {
      const requestBody = {
        title: document.getElementById("title").value,
        desc: document.getElementById("desc").value,
        date: document.getElementById("date").value,
        taskCategory: 1
      };
      const config = {
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        }
      };
      axios
        .post(
          "http://localhost:8080/api/task",
          qs.stringify(requestBody),
          config
        )
        .catch(function(error) {
          /* eslint-disable no-console */
          console.log(error);
        });
      this.intervalFetchData();
    },
    intervalFetchData: function() {
      setInterval(() => {
        this.APICall();
      }, 1000);
    }
  }
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css?family=Open+Sans");
$ease-out: all 0.3s cubic-bezier(0.23, 1, 0.32, 1);
$to-do: #f4ce46;
$in-progress: #485f69;
$approved: #00b961;

* {
  box-sizing: border-box;
}

body {
  background: white;
  color: #0e3047;
  font-family: "Open Sans";
  font-weight: 300;
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

.button-plus {
  bottom: 0;
  right: 0;
  z-index: 2147483000;
  position: fixed;
  margin-right: 20px;
  margin-bottom: 20px;
  width: 56px;
  height: 56px;

  h1 {
    font-size: 3.5rem;
  }
}

.badge {
  background-color: #84ca64 !important;
  border-radius: 0.15rem !important;
}

ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

.drag-container {
  max-width: 1200px;
  margin: 20px auto;
}

.drag-list {
  display: flex;
  align-items: flex-start;

  @media (max-width: 690px) {
    display: block;
  }
}

.drag-column {
  flex: 1;
  margin: 0 10px;
  position: relative;
  background: white;
  overflow: hidden;

  @media (max-width: 690px) {
    margin-bottom: 30px;
  }

  h1 {
    text-transform: uppercase;
    font-weight: 300;
  }

  &-to-do {
    .drag-column-header,
    .drag-options {
      background: $to-do;
    }
  }

  &-in-progress {
    .drag-column-header,
    .drag-options {
      background: $in-progress;
    }
  }

  &-approved {
    .drag-column-header,
    .drag-options {
      background: $approved;
    }
  }
}

.drag-column-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px;
  user-select: none;
  margin-bottom: -10px;
}

.drag-inner-list {
  height: 85vh;
  overflow: auto;
}

.drag-item {
  margin: 10px;
  height: 150px;
  background: white;
  transition: $ease-out;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  transition: 0.3s;
  border-radius: 5px;
  /* items grabbed state */
  &[aria-grabbed="true"] {
    background: gray;
    color: #fff;
  }
  .drag-item-large {
    height: 170px;
  }

  .drag-item-name {
    font-size: 1.4rem;
    padding-left: 1rem;
    padding-right: 1rem;
    padding-top: 1rem;
  }
  .drag-item-desc {
    font-size: 1rem;
    padding-left: 1rem;
    padding-right: 1rem;
    padding-top: 1rem;
  }
  .drag-item-date {
    font-size: 0.7rem;
    padding-left: 1rem;
    padding-right: 1rem;
    padding-top: 1rem;
    padding-bottom: 1rem;
    font-weight: 600;
  }
}

.drag-header-more {
  cursor: pointer;
}

@keyframes nodeInserted {
  from {
    opacity: 0.2;
  }
  to {
    opacity: 0.8;
  }
}

.item-dropzone-area {
  height: 6rem;
  background: #888;
  opacity: 0.8;
  animation-duration: 0.5s;
  animation-name: nodeInserted;
  margin-left: 0.6rem;
  margin-right: 0.6rem;
}
</style>
