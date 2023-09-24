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
          <input v-model="task" class="task-input" placeholder="Add tasks..." @keyup.enter="addTask" />
          <button @click="addTask" class="task-input-button">Add</button>
        </div>
      </div>
      
      <div class="notes-list">
        <div class="filter-buttons">
          <button class="filter-button" :class="{ 'active-filter': filter === 'all'}" @click="setFilter('all')">All</button>
          <button class="filter-button" :class="{ 'active-filter': filter === 'complete'}" @click="setFilter('complete')">Complete</button>
          <button class="filter-button" :class="{ 'active-filter': filter === 'incomplete'}" @click="setFilter('incomplete')">Incomplete</button>
        </div>
        <div class="notes-list-scroll-container">
          <div
          v-for="(task, index) in filteredTasks"          
          :key="index"
          :class="getTaskClass(task.status)"          
          draggable="true"
          @dragstart="startDrag(index, $event)"
          @dragend="endDrag"
          @mouseenter="hoveredItemIndex = index"
          @mouseleave="hoveredItemIndex = null"
          >
            <h3>{{ task.name }}</h3>
            
            <font-awesome-icon
              v-if="hoveredItemIndex === index && task.status === 'complete'"
              icon="hourglass-half"
              style="color: #d6d1d1; cursor: pointer;"
              @click="changeTaskStatus(task.id)"
            />
            <font-awesome-icon
              v-if="hoveredItemIndex === index && task.status === 'incomplete'"
              icon="check"
              style="color: #d6d1d1; cursor: pointer;"
              @click="changeTaskStatus(task.id)"
            />     
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
        },
        {
          id: 4,
          name: "task 4",
          status: 'incomplete'
        }
        
      ],
      filter: 'all',
      isInstructionDivVisible: true,
      draggedItemIndex: null,
      isBottomDivVisible: false,
      hoveredItemIndex: null,
    };
  },
  computed: {
    filteredTasks() {
      if (this.filter === 'all') {
        return this.tasks;
      } else if (this.filter === 'complete') {
        return this.tasks.filter(task => task.status === 'complete');
      } else if (this.filter === 'incomplete') {
        return this.tasks.filter(task => task.status === 'incomplete');
      }
      return [];
    },
  },
  methods: {
    addTask() {
      if (this.task.length !== 0) {
        this.tasks.push({
          id: this.tasks.length,
          name: this.task,
          status: 'incomplete'
        })
        this.task = ''
      }
    },
    getTaskClass(status) {
      return status === 'incomplete' ? 'red-list-item' : 'green-list-item';
    },
    setFilter(newFilter) {
      this.filter = newFilter;
    },
    closeDeleteInstructions() {
      this.isInstructionDivVisible = false;
    },
    startDrag(index, event) {
      this.draggedItemIndex = index;
      event.dataTransfer.setData('text/plain', index);
      this.isBottomDivVisible = true; // Show the bottom div when drag starts
    },
    removeItem(event) {
      event.preventDefault();
      const dropIndex = this.draggedItemIndex;
      if (dropIndex !== null) {
        this.tasks.splice(dropIndex, 1);
        this.draggedItemIndex = null;
        this.isBottomDivVisible = false; // Hide the bottom div after item removal
      }
    },
    endDrag() {
      this.isBottomDivVisible = false;
    },
    changeTaskStatus(id) {
      const taskToUpdate = this.tasks.find(task => task.id === id);
      // Update the task status when the icon is clicked
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

<!-- Add "scoped" attribute to limit CSS to this component only -->
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
  width: 100%;
  height: 30px;
  background-color: #383a3f;
  border: none;
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
  overflow: hidden; /* Hide the vertical overflow from the outer container */
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

.notes-list-scroll-container {
  width: 100%;
  height: 100%; /* Expand to fill the height of the .notes-list container */
  overflow-y: scroll; /* Enable scrolling for this container */
}

.green-list-item {
  border-left: 5px solid transparent; /* Transparent left border */
  border-right: 5px solid transparent; /* Transparent right border */
  border-top: 1px solid transparent; /* Transparent top border */
  border-bottom: 1px solid transparent; /* Transparent bottom border */
  border-image: linear-gradient(to right, rgb(108, 232, 86), transparent) 1 100%; /* Gradient border */
  border-image-slice: 1; /* Ensure the entire border is used as an image */
  margin: 12px;
  padding: 8px 16px;
  transition: all 0.2s ease-in-out;
  background: #1f2124; /* Transparent background */
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.green-list-item:hover {
  /* Change the gradient border from left to right when hovering */
  border-image: linear-gradient(to right, transparent, rgb(108, 232, 86)) 1 100%;
  border-left: 5px solid transparent; /* Transparent left border */
  border-right: 5px solid transparent; /* Transparent right border */
  border-top: 1px solid transparent; /* Transparent top border */
  border-bottom: 1px solid transparent; /* Transparent bottom border */
  border-image-slice: 1; /* Ensure the entire border is used as an image */
  margin: 12px;
  padding: 8px 16px;
  transition: all 0.2s ease-in-out;
  background: #1f2124; /* Transparent background */
  display: flex;
}

.red-list-item {
  border-left: 5px solid transparent; /* Transparent left border */
  border-right: 5px solid transparent; /* Transparent right border */
  border-top: 1px solid transparent; /* Transparent top border */
  border-bottom: 1px solid transparent; /* Transparent bottom border */
  border-image: linear-gradient(to right, rgb(255, 56, 56), transparent) 1 100%; /* Gradient border */
  border-image-slice: 1; /* Ensure the entire border is used as an image */
  margin: 12px;
  padding: 8px 16px;
  transition: all 0.2s ease-in-out;
  background: #1f2124; /* Transparent background */
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.red-list-item:hover {
  /* Change the gradient border from left to right when hovering */
  border-image: linear-gradient(to right, transparent, rgb(255, 56, 56)) 1 100%;
  border-left: 5px solid transparent; /* Transparent left border */
  border-right: 5px solid transparent; /* Transparent right border */
  border-top: 1px solid transparent; /* Transparent top border */
  border-bottom: 1px solid transparent; /* Transparent bottom border */
  border-image-slice: 1; /* Ensure the entire border is used as an image */
  margin: 12px;
  padding: 8px 16px;
  transition: all 0.2s ease-in-out;
  background: #1f2124; /* Transparent background */
  display: flex;
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
  opacity: 0; /* Initially hidden */
  transition: opacity 0.3s; /* Smooth opacity transition */
}

.visible {
  opacity: 1; /* Show the bottom div */
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

/* Style for the close button */
.close-button {
  background: none;
  border: none;
  cursor: pointer;
}

</style>
