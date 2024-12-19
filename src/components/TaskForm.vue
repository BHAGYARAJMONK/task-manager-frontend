<template>
  <div class="container mt-5">
    <h1>Create Task</h1>
    <form @submit.prevent="submitTask" class="w-50 mx-auto">
      <div class="mb-3">
        <label for="taskTitle" class="form-label">Title:</label>
        <input type="text" id="taskTitle" v-model="task.title" class="form-control" />
      </div>
      <div class="mb-3">
        <label for="taskPriority" class="form-label">Priority:</label>
        <select id="taskPriority" v-model="task.priority" class="form-select">
          <option value="low">Low</option>
          <option value="medium">Medium</option>
          <option value="high">High</option>
        </select>
      </div>
      <div class="mb-3">
        <label for="taskDueDate" class="form-label">Due Date:</label>
        <input type="date" id="taskDueDate" v-model="task.due_date" class="form-control" />
      </div>
      <button type="submit" class="btn btn-primary">Submit</button>
    </form>

    <!-- Success message -->
    <div v-if="message" class="alert alert-success mt-3">
      {{ message }}
    </div>

    <!-- Error messages -->
    <div v-if="errors.length" class="alert alert-danger mt-3">
      <ul>
        <li v-for="error in errors" :key="error">{{ error }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      task: {
        title: '',
        priority: 'low',
        due_date: '',
      },
      message: '',
      errors: [],
    };
  },
  methods: {
    // Function to validate inputs
    validateForm() {
      this.errors = [];

      // Check if all fields are filled
      if (!this.task.title || !this.task.due_date) {
        this.errors.push("All fields must be filled.");
      }

      // Check if due_date is a valid date and not in the past
      const dueDate = new Date(this.task.due_date);
      const currentDate = new Date();

      if (dueDate < currentDate) {
        this.errors.push("Due date cannot be in the past.");
      }

      // If there are errors, return false
      if (this.errors.length > 0) {
        return false;
      }
      return true;
    },

    // Submit form
    async submitTask() {
      // Validate form inputs
      if (!this.validateForm()) {
        return;
      }

      this.message = '';
      try {
        const response = await axios.post('http://127.0.0.1:8000/api/tasks', this.task);
        this.message = response.data.message;
        this.task = { title: '', priority: 'low', due_date: '' };
      } catch (error) {
        if (error.response && error.response.data.errors) {
          this.errors = Object.values(error.response.data.errors).flat();
        } else {
          this.errors = ['An unexpected error occurred.'];
        }
      }
    },
  },
};
</script>
