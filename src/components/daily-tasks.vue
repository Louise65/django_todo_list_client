<template>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
    <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Raleway">
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,300,0,0" />
    <main>
    <div class="w3-bar w3-top w3-black w3-large">
        <span class="w3-bar-item w3-left">
            &nbsp;TO DO APP
        </span>
        
        <span class="w3-bar-item w3-right">
            <button id="myBtn" class="w3-button w3-dark-grey" @click="createAdd()">Create New Task</button>
        </span>
        
    </div>
    <div class="w3-main margin-left:300px;margin-top:43px;">
        <!-- Daily Task Container -->
        <div class = "w3-container">
            <h1>Daily Tasks</h1>
            <table class="w3-table w3-striped w3-bordered w3-border w3-hoverable w3-white">
                <thead>
                    <tr>
                        <th bgcolor="#AAAAAA">Task Name</th>
                        <th bgcolor="#AAAAAA">Start Time</th>
                        <th bgcolor="#AAAAAA">End Time</th>
                        <th bgcolor="#AAAAAA">Priority</th>
                        <th bgcolor="#AAAAAA">Status</th>
                        <th bgcolor="#AAAAAA" style="text-align: center">Done?</th>
                        <th bgcolor="#AAAAAA" style="text-align: center">Update</th>
                        <th bgcolor="#AAAAAA" style="text-align: center">Delete</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="dailytask in dailyTasks" :key="dailytask.dailyTaskID">
                        <td>
                            <span>
                                {{ dailytask.dailyTask_name }}
                            </span>
                        </td>
                        <td>
                            <span>
                                {{ turnTo12HrFormat(dailytask.dailyStartTime) }}
                            </span>
                        </td>
                        <td>
                            <span>
                                {{ turnTo12HrFormat(dailytask.dailyEndTime) }}
                            </span>
                        </td>
                        <td>
                            {{ determinePriority(dailytask.dailyTaskPriority) }}
                        </td>
                        <td>
                            <!-- Not started -->
                            <span v-if="checkStatus(dailytask.dailyStartTime, dailytask.dailyEndTime, dailytask.dailyTaskStatus) == 'Not Started'">
                                {{ checkStatus(dailytask.dailyStartTime, dailytask.dailyEndTime, dailytask.dailyTaskStatus) }}
                            </span>
                            <!-- Ongoing -->
                            <span class = "w3-text-orange" v-else-if="checkStatus(dailytask.dailyStartTime, dailytask.dailyEndTime, dailytask.dailyTaskStatus) == 'Ongoing'">
                                {{ checkStatus(dailytask.dailyStartTime, dailytask.dailyEndTime, dailytask.dailyTaskStatus) }}
                            </span>
                            <!-- Late -->
                            <span class = "w3-text-red" v-else-if="checkStatus(dailytask.dailyStartTime, dailytask.dailyEndTime, dailytask.dailyTaskStatus) == 'Late'">
                                {{ checkStatus(dailytask.dailyStartTime, dailytask.dailyEndTime, dailytask.dailyTaskStatus) }}
                            </span>
                            <!-- Completed -->
                            <span class = "w3-text-green" v-else-if="checkStatus(dailytask.dailyStartTime, dailytask.dailyEndTime, dailytask.dailyTaskStatus) == 'Completed'">
                                {{ checkStatus(dailytask.dailyStartTime, dailytask.dailyEndTime, dailytask.dailyTaskStatus) }}
                            </span>
                        </td>
                        <td style="text-align: center">
                            <button @click="toggleTask(dailytask)">
                                {{ dailytask.dailyTaskStatus ? 'Done' : 'Not Done' }}
                            </button>
                        </td>
                        <td style="text-align: center">
                            <button @click="updateAdd(dailytask)">
                                <i class="material-symbols-outlined">edit</i>
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
        </div>
    </div>
    </main>
    <!-- Add new daily Task -->
    <div v-if="showAddModal" class="modal">
        <!-- Modal content -->
        <div id="addNewModal" class="modal-content">
            <span class="close" @click="closeAddModal">&times;</span>
            <h1>{{ modalTitle }}</h1>
                <label for="daily-task-name">Task Title: </label>
                <input name="daily-task-name" id="daily-task-name" v-model="dailyTask_name" required/>
                <br>

                <label for="daily-time-hour">Starting Time: </label> 
                <input type="time" id="daily-start-time" name="daily-start-time" v-model="dailyStartTime" required>
                <br>
                <label for="daily-time-min">Ending time</label> 
                <input type="time" id="daily-end-time" name="daily-end-time" v-model="dailyEndTime" required>
                <br>

                <label for="priority">Priority: </label>
                <select id="priority" v-model="dailyTaskPriority" required>
                    <option value="3" selected>Low</option>
                    <option value="2">Medium</option>
                    <option value="1">High</option>
                </select>
                <br>

                <button v-if="dailyTaskID=0" @click = "addTask()">
                    Save daily task
                </button>
                <button v-if="dailyTaskID=!0" @click = "updateTask()">
                    Save changes to task
                </button>
        </div>
    </div>
</template>

<style>
    /* @import url('css/modal.css'); */
</style>

<script>
    export default {
        data() {
            return {
                // tasks
                dailyTasks: [''],
                dailyTaskID: 0,
                dailyTask_name: '',
                dailyStartTime: '',
                dailyEndTime: '',
                dailyTaskPriority: 3,

                //modal stuff
                modalTitle: "",
                showAddModal: false,
                showUpdateModal: false
            }
        },
        methods: {
            //modal stuff
            openAddModal() {
                this.showAddModal = true;
            },
            closeAddModal() {
                this.showAddModal = false;

                this.dailyTaskID = 0;
                this.dailyTask_name = '';
                this.dailyStartTime = '';
                this.dailyEndTime = '';
                this.dailyTaskPriority = 3;
            },
            //database stuff
            createAdd(){
                // Modal Stuff
                this.modalTitle = "Add new Task";

                this.dailyTaskID = 0;
                this.dailyTask_name = '';
                this.dailyStartTime = '';
                this.dailyEndTime = '';
                this.dailyTaskPriority = 3; 

                this.openAddModal();
                console.log(this.dailyTaskID);
            },
            updateAdd(task){
                // Modal Stuff
                this.modalTitle = "Update Task";

                this.dailyTaskID = task.dailyTaskID;
                this.dailyTask_name = task.dailyTask_name;
                this.dailyStartTime = task.dailyStartTime;
                this.dailyEndTime = task.dailyEndTime;
                this.dailyTaskPriority = task.dailyTaskPriority;    

                this.openAddModal();
                console.log(this.dailyTaskID);
            },
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
            async createTask(){
                try {
                    //if start time is greater than end time
                    if (this.dailyStartTime > this.dailyEndTime){
                        let temp = this.dailyStartTime;
                        this.dailyStartTime = this.dailyEndTime;
                        this.dailyEndTime = temp;
                    }
                    // Send a POST request to the API
                    const response = await this.$http.post('http://localhost:8000/todolist/dailytasks/', {
                        dailyTask_name: this.dailyTask_name,
                        dailyStartTime: this.dailyStartTime,
                        dailyEndTime: this.dailyEndTime,
                        dailyTaskPriority: this.dailyTaskPriority,
                    });
                    this.tasks.push(response.data);
                } catch (error) {
                    console.log(error);
                }
                // Reset stuff
                this.dailyTaskID = 0;
                this.dailyTask_name = '';
                this.dailyStartTime = '';
                this.dailyEndTime = '';
                this.dailyTaskPriority = 3;
                //refresh table
                this.getData();
                // Close modal
                this.closeAddModal();
            },
            async updateTask(task){
                try {
                    //if start time is greater than end time
                    if (this.dailyStartTime > this.dailyEndTime){
                        let temp = this.dailyStartTime;
                        this.dailyStartTime = this.dailyEndTime;
                        this.dailyEndTime = temp;
                    }
                    // Send a PUT request to the API
                    const response = await this.$http.put(`http://localhost:8000/todolist/dailytasks/${task.dailyTaskID}/`, {
                        dailyTask_name: this.dailyTask_name,
                        dailyStartTime: this.dailyStartTime,
                        dailyEndTime: this.dailyEndTime,
                        dailyTaskPriority: this.dailyTaskPriority,
                    });
                    this.tasks.push(response.data);
                } catch (error) {
                    console.log(error);
                }
                // Reset stuff
                this.dailyTask_name = '';
                this.dailyStartTime = '';
                this.dailyEndTime = '';
                //refresh table
                this.getData();
                // Close modal
                this.closeUpdateModal();
            },
            async toggleTask(task){
                try{
                    const response = await this.$http.put(`http://localhost:8000/todolist/dailytasks/${task.dailyTaskID}/`, {
                        dailyTaskStatus: !task.dailyTaskStatus,
                        dailyTask_name: task.dailyTask_name,
                        dailyStartTime: task.dailyStartTime,
                        dailyEndTime: task.dailyEndTime
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
            },
            turnTo12HrFormat(timeOrig){
                // Check correct time format and split into components
                let time = (timeOrig + "").substring(0,5);
                time = time.match(/^([01]\d|2[0-3])(:)([0-5]\d)(:[0-5]\d)?$/) || [time];

                if (time.length > 1) { // If time format correct
                    time = time.slice(1); // Remove full string match value
                    time[5] = +time[0] < 12 ? ' AM' : ' PM'; // Set AM/PM
                    time[0] = +time[0] % 12 || 12; // Adjust hours
                }

                return time.join(''); // return adjusted time or original string
            },
            checkStatus(timeStartOrig, timeEndOrig, status){
                let timeStart = (timeStartOrig + "");
                let timeEnd = (timeEndOrig + "");

                let timeStartTru = parseInt((timeStart.split(":")[0])) * 60 + parseInt((timeStart.split(":")[1]));

                let timeEndTru = parseInt((timeEnd.split(":")[0])) * 60 + parseInt(timeEnd.split(":")[1]);

                let date = new Date();
                let currTime = (date.getHours() * 60) + date.getMinutes();

                if (status){
                    return "Completed"
                } else {
                    if (timeStartTru > currTime){
                        return "Not Started"
                    } else if (timeStartTru <= currTime && currTime <= timeEndTru){
                        return "Ongoing"
                    } else if (timeEndTru < currTime){
                        return "Late"
                    }
                }
            },
            determinePriority(priority){
                switch (priority){
                    case 3:
                        return "Low";
                    case 2:
                        return "Medium";
                    case 1:
                        return "High";
                    default:
                        return "None";
                }
            }
        },
        created() {
            // Fetch tasks on page load
            this.getData();
        }
    }

</script>