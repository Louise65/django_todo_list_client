<template>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
    <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Raleway">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,300,0,0" />
    <div class="w3-panel">
        <!-- Daily Task Container -->
        <div class="w3-twothird">
            <h1>Daily Tasks</h1>
            <ul class="tasks_list">
                <table class="w3-table w3-striped w3-white">
                    <thead>
                        <tr>
                            <th bgcolor="#AAAAAA">Task Name</th>
                            <th bgcolor="#AAAAAA">Time</th>
                            <th bgcolor="#AAAAAA" style="text-align: center">Status</th>
                            <th bgcolor="#AAAAAA" style="text-align: center">Delete</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="dailytask in dailyTasks" :key="dailytask.dailyTaskID">
                            <td>
                                <!-- Color it green if done -->
                                <span v-if="dailytask.dailyTaskStatus == true">
                                    <span style="color:green">
                                        {{ dailytask.dailyTask_name }}
                                    </span>
                                </span>
                                <!-- Color it orange if not done and in current hour -->
                                <span v-else-if="(dailytask.dailyTimeHr == (`${new Date().getHours()}`)) && (dailytask.dailyTaskStatus == false)">
                                    <span style="color:orange">
                                        {{ dailytask.dailyTask_name }}
                                    </span>
                                </span>
                                <!-- Color it red if not done and late -->
                                <span v-else-if="(dailytask.dailyTimeHr < (`${new Date().getHours()}`)) && (dailytask.dailyTaskStatus == false)">
                                    <span style="color:red">
                                        {{ dailytask.dailyTask_name }}
                                    </span>
                                </span>
                                <!-- Color it black if it's upcoming -->
                                <span v-else>
                                    {{ dailytask.dailyTask_name }}
                                </span>
                            </td>
                            <td>
                                <!-- Color it green if done -->
                                <span v-if="dailytask.dailyTaskStatus == true">
                                    <span style="color:green">
                                        {{("0" + dailytask.dailyTimeHr).slice(-2)}}:
                                        {{("0" + dailytask.dailyTimeMin).slice(-2)}}
                                    </span>
                                </span>
                                <!-- Color it orange if not done and in current hour -->
                                <span v-else-if="(dailytask.dailyTimeHr == (`${new Date().getHours()}`)) && (dailytask.dailyTaskStatus == false)">
                                    <span style="color:orange">
                                        {{("0" + dailytask.dailyTimeHr).slice(-2)}}:
                                        {{("0" + dailytask.dailyTimeMin).slice(-2)}}
                                    </span>
                                </span>
                                <!-- Color it red if not done and late -->
                                <span v-else-if="(dailytask.dailyTimeHr < (`${new Date().getHours()}`)) && (dailytask.dailyTaskStatus == false)">
                                    <span style="color:red">
                                        {{("0" + dailytask.dailyTimeHr).slice(-2)}}:
                                        {{("0" + dailytask.dailyTimeMin).slice(-2)}}
                                    </span>
                                </span>
                                <!-- Color it black if it's upcoming -->
                                <span v-else>
                                    {{("0" + dailytask.dailyTimeHr).slice(-2)}}:
                                        {{("0" + dailytask.dailyTimeMin).slice(-2)}}
                                </span>
                            </td>
                            <td style="text-align: center">
                                <button @click="toggleTask(dailytask)">
                                    {{ dailytask.dailyTaskStatus ? 'Done' : 'Not Done' }}
                                </button>
                            </td>
                            <td style="text-align: center">
                                <button @click="deleteTask(dailytask)">
                                    <i class="material-symbols-outlined">delete_forever</i>
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </ul>
        </div>
        <!-- Add new daily Task -->
        <div class="w3-third">
            <h1>Add New Daily Task</h1>
            <form v-on:submit.prevent="submitForm">
                <div class="form-group">
                    <label for="daily-task-name">Task Title: </label>
                    <input name="daily-task-name" id="daily-task-name" v-model="dailyTask_name" />
                </div>
                <div class="form-group">
                    <label for="daily-time-hour">Time: </label> 
                    <input name="daily-time-hour" type="number" min="0" max="23" value="8" id="daily-time-hour" v-model="dailyTimeHr" />
                    <label for="daily-time-min">:</label> 
                    <input name="daily-time-min" type="number" min="0" max="59" value="00" id="daily-time-min" v-model="dailyTimeMin"/>
                </div>
                <div class="form-group">
                    <button>
                        Save daily Task
                    </button>
                </div>
            </form>
        </div>
    </div>
</template>

<script>

    export default {
        data() {
            return {
                // tasks
                dailyTasks: [''],
                dailyTask_name: '',
                dailyTimeHr: '',
                dailyTimeMin: '',
            }
        },
        methods: {
            async getData() {
                try {
                    // fetch tasks
                    const response = await this.$http.get('http://localhost:8000/todolist/dailytasks/');
                    // set the data returned as tasks
                    this.dailyTasks = response.data;
                } catch (error) {
                    console.log(error);
                }
            },
            async submitForm(){
                try {
                    // Send a POST request to the API
                    const response = await this.$http.post('http://localhost:8000/todolist/dailytasks/', {
                        dailyTask_name: this.dailyTask_name,
                        dailyTimeHr: this.dailyTimeHr,
                        dailyTimeMin: this.dailyTimeMin
                    });
                    this.tasks.push(response.data);
                    // Reset stuff
                    this.dailyTask_name = '';
                    this.dailyTimeHr = '';
                    this.dailyTimeMin = '';
                } catch (error) {
                    console.log(error);
                }
                //refresh table
                this.getData();
            },
            async toggleTask(task){
                try{
                    const response = await this.$http.put(`http://localhost:8000/todolist/dailytasks/${task.dailyTaskID}/`, {
                        dailyTaskStatus: !task.dailyTaskStatus,
                        dailyTask_name: task.dailyTask_name,
                        dailyTimeHr: task.dailyTimeHr,
                        dailyTimeMin: task.dailyTimeMin
                    });

                    let taskIndex = this.tasks.findIndex(t => t.id === task.dailyTaskID);
                    this.tasks = this.tasks.map((task) => {
                        if(this.tasks.findIndex(t => t.dailyTaskID === task.dailyTaskID) === taskIndex){
                            return response.data;
                        }
                        return task;
                    });
                }catch(error){
                    console.log(error);
                }
                //refresh table
                this.getData();
            },
            async deleteTask(task){
                let confirmation = confirm("Do you want to delete this task?"); 
                if(confirmation){
                    try{
                        await this.$http.delete(`http://localhost:8000/todolist/dailytasks/${task.dailyTaskID}/`);

                        // refresh table
                        this.getData();
                    } catch(error){
                        console.log(error)
                    }
                }      
            }
        },
        created() {
            // Fetch tasks on page load
            this.getData();
        }
    }

</script>