<template>
    <div class="container">
        <div class="col-sm-offset-2 col-sm-8">
            <div class="panel panel-default">
                <div class="panel-heading">
                    New Task
                </div>

                <div class="panel-body">

                    <error-list v-if="hasErrors" :errors="errors"></error-list>

                    <!-- New Task Form -->
                    <form @submit.prevent="add" class="form-horizontal">

                        <!-- Task Name -->
                        <div class="form-group">
                            <label for="task-name" class="col-sm-3 control-label">Task</label>

                            <div class="col-sm-6">
                                <input type="text" id="task-name" class="form-control" v-model="form.name">
                            </div>
                        </div>

                        <!-- Add Task Button -->
                        <div class="form-group">
                            <div class="col-sm-offset-3 col-sm-6">
                                <button type="submit" class="btn btn-default">
                                    <i class="fa fa-btn fa-plus"></i>Add Task
                                </button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>

            <!-- Current Tasks -->
            <div v-if="hasTasks" class="panel panel-default">
                <div class="panel-heading">
                    Current Tasks
                </div>

                <div class="panel-body">
                    <table class="table table-striped task-table">
                        <thead>
                            <th>Task</th>
                            <th>&nbsp;</th>
                        </thead>
                        <tbody>
                        <tr v-for="task in view.tasks" :key="task.id">
                            <td class="table-text"><div>{{ task.name }}</div></td>

                            <!-- Task Delete Button -->
                            <td>
                                <button @click.prevent.stop="remove(task.id)" type="submit" class="btn btn-danger">
                                    <i class="fa fa-btn fa-trash"></i>Delete
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
    import ErrorList from '../components/ErrorList'

    export default {
        components: {
            ErrorList
        },
        data () {
            return {
                form: {
                    name: ''
                },
                view: {
                    tasks: []
                },
                errors: [],
            }
        },
        created () {
            this.fetchTasks();
        },
        computed: {
            hasTasks () {
                return this.view.tasks.length > 0;
            },
            hasErrors () {
                return this.errors.length > 0; 
            }
        },
        methods: {
            fetchTasks () {
                axios.get('/api/task')
                    .then(response => {
                        this.view.tasks = response.data;
                    });
            },
            add () {
                this.resetError();

                axios.post('/api/task', this.form)
                    .then(() => {
                        this.fetchTasks();
                        this.resetForm();
                    })
                    .catch(error => {
                        this.errors = error.response.data.errors.name;
                    });
            },
            remove (id) {
                axios.delete('/api/task/' + id)
                    .then(() => {
                        this.fetchTasks();
                    })
                    .catch(error => {
                        console.log(error)
                    });
            },
            resetForm () {
                this.form.name = '';
            },
            resetError () {
                this.errors = [];
            }
        }
    }
</script>
