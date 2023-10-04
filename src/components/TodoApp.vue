<template>
  <div class="app">
    <div
      class="app-container"
      >
      <div class="header">
        <div class="app-header">
          <h1>Tasks</h1>
        </div>
        <div class="task-input-div">
          <input v-model="task" class="task-input" placeholder="Add tasks..." />
           <input v-model="status" class="status-input" placeholder="Mention the status" @keyup.enter="addTask" />
          <button @click="addTask" class="task-input-button">Add</button>
        </div>
      </div>
      
      <div class="notes-list">
        <div class="filter-buttons">
          <h3>Filter: </h3>
          <select 
          name="filters"
          v-model="filter"
          @change="onChange($event.target.value)"
          class="filters-select"
          >
            <option value="">All</option>
            <option v-for="(status, index) in uniqueStatuses"
            :key="index"
            :value="status"
            >
            {{status}}
            </option>
          </select>
        </div>
        <div class="notes-list-scroll-container">
          <div
          v-for="(task, index) in filteredTasks"          
          :key="index"
          class="list-item"          
          draggable="true"
          @dragstart="startDrag(index, $event)"
          @dragend="endDrag"
          @mouseenter="hoveredItemIndex = index"
          @mouseleave="hoveredItemIndex = null"
          >
            <div
            class="list-item-text"
            >
              <h3>{{ task.name }} - {{ task.status }}</h3>
              <font-awesome-icon icon="pen-to-square" 
                style="color: #d6d1d1; cursor: pointer;"
                @click="editTask(task.id)"/>
            </div>
            <div
              class="list-item-input"
            >
              <input 
              v-if="isEditing(task.id)"
              v-model="updateStatus"
              class="status-input"
              placeholder="Mention the status"
              @keyup.enter="updateTask(task.id)"
              />
              <button 
              v-if="isEditing(task.id)"
              @click="updateTask(task.id)"
              class="task-input-button"
              >Update</button>
            </div>

          </div>
          <div class="empty-space"></div>
        </div>
      </div>
      <div class="bottom-div"
        @dragover.prevent 
        @drop="removeItem"
        :class="{ 'visible': isBottomDivVisible }"
        id="droptarget"
      >
        <font-awesome-icon :icon="['fas', 'trash']" style="color: #ffffff;" />
      </div>
    </div>
    <div class="delete-instructions" v-if="isInstructionDivVisible">
      <div class="delete-message">
        <p>Drag and drop to delete tasks.</p>
      </div>
      <font-awesome-icon @click="closeDeleteInstructions" icon="circle-xmark" style="color: #acb4bd; cursor: pointer" />
      
    </div>
  </div>
</template>

<script>
import "../styles/customScrollbar.css";

export default {
  
  data() {
    return {
      task: '',
      status: '',
      updateStatus : '',
      tasks: [
        {
          id: 1,
          name: "task 1",
          status: 'incomplete'
        },
        {
          id: 2,
          name: "task 2",
          status: 'complete'
        },
        {
          id: 3,
          name: "task 3",
          status: 'complete'
        }
      ],
      filter: '',
      editingIndex: -1,
      isInstructionDivVisible: true,
      draggedItemIndex: null,
      isBottomDivVisible: false,
      hoveredItemIndex: null,
    };
  },
  computed: {
    uniqueStatuses() {
      // Use a Set to store unique status values
      const uniqueStatusesSet = new Set();
      
      // Iterate through the tasks and add unique status values to the Set
      this.tasks.forEach(task => {
        uniqueStatusesSet.add(task.status);
      });
      
      // Convert the Set back to an array and return it
      return Array.from(uniqueStatusesSet);
    },
    filteredTasks() {
      if (this.filter === '') {
        return this.tasks
      }
      return this.tasks.filter(task => task.status === this.filter);
    },
  },
  methods: {
    onChange:function(val) {
      this.filter = val;
    },
    isEditing(index) {
      return this.editingIndex === index;
    },
    editTask(index) {
      if (this.editingIndex !== -1 && this.editingIndex === index) {
        this.editingIndex = -1;
      }
      else {
        this.editingIndex = index;
      }
    },
    addTask() {
      if (this.task.length !== 0 && this.status.length !== 0) {
        this.tasks.push({
          id: this.tasks.length > 0 ? this.tasks[this.tasks.length-1]["id"] + 1 : 0,
          name: this.task,
          status: this.status
        })
        this.status = ''
        this.task = ''
      }
    },
    updateTask(id) {
      const taskToUpdate = this.tasks.find(task => task.id === id);
      if (taskToUpdate) {
        taskToUpdate.status = this.updateStatus;
      }
      this.updateStatus = '';
      this.filter = taskToUpdate.status;
    },
    setFilter(newFilter) {
      console.log(newFilter)
      this.filter = newFilter;
    },
    closeDeleteInstructions() {
      this.isInstructionDivVisible = false;
    },
    startDrag(index, event) {
      this.draggedItemIndex = index;
      event.dataTransfer.setData('text/plain', index);
      this.isBottomDivVisible = true; 
    },
    removeItem(event) {
      event.preventDefault();
      const dropIndex = this.draggedItemIndex;
      if (dropIndex !== null) {
        this.tasks.splice(dropIndex, 1);
        this.draggedItemIndex = null;
        this.isBottomDivVisible = false; 
      }
    },
    endDrag() {
      this.isBottomDivVisible = false;
    },
    changeTaskStatus(id) {
      const taskToUpdate = this.tasks.find(task => task.id === id);
      if (taskToUpdate) {
        if (taskToUpdate.status === 'complete') {
          taskToUpdate.status = 'incomplete';
        }
        else {
          taskToUpdate.status = 'complete';
        }
      }
    },
  },
};
</script>

<style scoped>
.app {
  width: 100%;
  max-width: 480px;
  height: 88vh;
  margin: 0 auto;
  background-color: var(--color-white);
  box-shadow: 1px 1px 6px rgba(0, 0, 0, 0.05);
  position: relative;
}

.app-container {
  height: 100%;
  display: flex;
  flex-direction: column;
}
.header {
  flex:1;
  display: flex;
  flex-direction: column;
}

.app-header {
  display: flex;
  align-items: center;
  padding: 16px;
  justify-content: space-between;
  background-color: var(--color-lighter);
  box-shadow: 0px 1px 3px rgba(0, 0, 0, 0.1);
}

.task-input-div {
  display: flex;
  align-items: center;
  padding: 16px;
  justify-content: center;
  background-color: var(--color-lighter);
  box-shadow: 0px 1px 3px rgba(0, 0, 0, 0.1);
}

.task-input {
  flex: 4;
  height: 30px;
  background-color: #383a3f;
  border: #212225;
  margin-right: 2px;
  padding: 5px;
  color: #d6d1d1;
}

.status-input {
  flex: 1;
  height: 30px;
  background-color: #383a3f;
  border: 1px #212225;
  padding: 5px;
  color: #d6d1d1;
}

.task-input-button {
  height: 30px;
  padding: 0px 20px;
  background-color: #f68657;
  border: none;
  color: #1f2124;
}

.notes-list {
  flex: 5;
  padding: 0px;
  margin: 10px;
  overflow: hidden; 
}

.filter-buttons {
  display: flex;
  width: 100%;
  justify-content: space-around;
  align-items: center;
}

.filter-button {
  background-color: #acb4bd;
  border: none;
  width: 20%;
  padding: 5px 0px;
  cursor: pointer;
}

.active-filter {
  border: 3px solid #f68657;
}

.filters-select {
  height: 30px;
  background-color: #383a3f;
  border: 1px solid #212225; /* Added border style */
  margin-right: 2px;
  padding: 5px;
  color: #d6d1d1;
}

.notes-list-scroll-container {
  width: 100%;
  height: 100%;
  overflow-y: scroll; 
}

.list-item {
  margin: 12px;
  padding: 8px 16px;
  transition: all 0.2s ease-in-out;
  background: #1f2124; 
  display: flex;
  align-items: center;
  /* justify-content: space-between; */
  flex-direction: column;
}

.list-item:hover {
  margin: 12px;
  padding: 8px 16px;
  transition: all 0.2s ease-in-out;
  background: #1f2124; 
  display: flex;
}

.list-item-input {
  width: 80%;
  display: flex;
  align-items: center;
}

.list-item-text {
  width: 100%;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.empty-space {
  margin-bottom: 55px;
}

.bottom-div {
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 50px;
  background: linear-gradient(to top, rgba(255, 50, 50, 1), rgba(255, 50, 50, 0)); /* Gradient from bottom to top */
  opacity: 0;
  transition: opacity 0.3s;
}

.visible {
  opacity: 1; 
}

.delete-instructions {
  position: fixed;
  bottom: 10px;
  right: 10px;
  background-color: rgba(0, 0, 0, 0);
  color: #acb4bd;
  border: 2px solid #acb4bd;
  border-radius: 5px;
  padding: 10px;
  display: flex;
  align-items: center;
}

/* Style for the delete message */
.delete-message {
  flex-grow: 1;
  padding-right: 10px;
}

</style>
