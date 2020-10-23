<template>
  <div class="todo">
    <div class="card text-left">
      <button type="button" class="btn btn-outline-primary" data-toggle="modal" data-target="#exampleModal"
              data-whatever="@mdo">Add new task
      </button>

      <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel"
           aria-hidden="true">
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLabel">New message task</h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              <form>
                <div class="form-group">
                  <label for="task-title">Task title</label>
                  <input v-model="model.title" type="text" class="form-control" id="task-title"
                         aria-describedby="emailHelp">
                </div>
                <div class="form-group">
                  <label for="task-desc">Task description</label>
                  <textarea v-model="model.description" class="form-control" id="task-desc"></textarea>
                </div>

                <div class="dropdown">
                  <button class="btn" :class="{'btn btn-outline-primary' :model.types==='Primary',
                                               'btn btn-outline-secondary':model.types==='Secondary',
                                               'btn btn-outline-danger':model.types==='Urgent'}"
                          type="button"
                          data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                    {{ model.types }}
                  </button>

                  <div class="dropdown-menu" aria-labelledby="dropdownMenuButton">
                    <button class="btn btn-outline-primary" @click.prevent="model.types='Primary'" href="#">Primary
                    </button>
                    <br/>
                    <button class="btn btn-outline-secondary" @click.prevent="model.types='Secondary'" href="#">
                      Secondary
                    </button>
                    <br/>
                    <button class="btn btn-outline-danger" @click.prevent="model.types='Urgent'" href="#">Urgent
                    </button>
                  </div>
                </div>
                <br/>
              </form>

            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
              <button type="button" class="btn btn-primary" @click.prevent="submit" :disabled="!isValid">Submit</button>
            </div>
          </div>
        </div>
      </div>


      <div class="card-header">
        ToDo Component
      </div>
      <div class="card-body">
        <h5 class="card-title">My tasks</h5>
        <div class="card-text">
          <ul class="pt-3">
            <li v-for="(item, index) in filteredTaskList" :key="index" class="d-flex justify-content-between mb-3">
              <div>
                <h4 v-if="item.status === 'completed'"><s>{{ item.title }}</s></h4>
                <h4 v-else>{{ item.title }}</h4>
                <p>{{ item.description }}</p>
                <div class="dropdown">
                  <button class="btn" :class="{'btn btn-outline-primary' :model.types==='Primary',
                                               'btn btn-outline-secondary':model.types==='Secondary',
                                               'btn btn-outline-danger':model.types==='Urgent'}"
                          type="button"
                          data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                    {{ model.types }}
                  </button>

                  <div class="dropdown-menu" aria-labelledby="dropdownMenuButton">
                    <button class="btn btn-outline-primary" @click.prevent="model.types='Primary'" href="#">Primary
                    </button>
                    <br/>
                    <button class="btn btn-outline-secondary" @click.prevent="model.types='Secondary'" href="#">
                      Secondary
                    </button>
                    <br/>
                    <button class="btn btn-outline-danger" @click.prevent="model.types='Urgent'" href="#">Urgent
                    </button>
                  </div>
                </div>


              </div>
              <button @click="handleRemove(index)" type="button"
                      class="btn btn-outline-danger btn-sm float-right mb-5 mt-5 ">Delete
              </button>
              <button class="btn btn-outline-primary float-right mb-5 mt-5 " @click="item.status = 'completed' ">
                Compled
              </button>
            </li>
          </ul>
        </div>
        <div class="card-footer">
          <button class="btn" :class="{'btn-primary':!filterStatus}" @click="filterStatus = ''">All</button>
          <button class="btn" :class="{'btn-primary': filterStatus === 'completed'}"
                  @click="filterStatus = 'completed'">Completed
          </button>
          <button class="btn" :class="{'btn-primary': filterStatus === 'incompleted'}"
                  @click="filterStatus = 'incompleted'">InCompleted
          </button>
          Active tasks count: {{ taskList.length }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {Task} from '../models/task';

export default {
  name: "ToDo",
  props: {},
  data: () => {
    return {
      model: new Task(),
      taskList: [],
      filterStatus: "",
    }
  },
  async mounted() {
    this.loading = true;
    this.taskList = await this.$services.todo.fetch();
    this.loading = false;
  },
  methods: {
    submit() {

        this.taskList.push(this.model);
        this.model = new Task();

    },
    handleRemove(index) {
      this.taskList.splice(index, 1);
    }
  },
  watch: {
    message() {
      console.log("Nessage changed");
    }
  },
  computed: {
    messageLength() {
      return ("" + this.message).length;
    },
    isValid() {
      return this.model.title && this.model.description && this.model.types;
    },
    filteredTaskList() {
      return this.taskList.filter((item) => {
        if (!this.filterStatus) return true;
        return item.status === this.filterStatus;
      });
    }
  }

}
</script>

<style scoped>
.todo {
  background-color: aqua;
}
</style>
