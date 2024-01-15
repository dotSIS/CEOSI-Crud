<template>
  <div id="app" class="max-w-2xl mx-auto">
    <h1>Students C.R.U.D</h1>
    {{  apiLog }} <br>
    <button class="button button-green" @click="addStudent()">+Add</button> <br>

    <div v-for="(student, index) in students" :key="index" class="flex mb-4 items-center">
      <!-- Display student information -->
      <input class="input" placeholder="id" size="1" type="text" v-model="student.id" disabled>
      <input class="input" placeholder="first_name" type="text" v-model="student.first_name">
      <input class="input" placeholder="last_name" type="text" v-model="student.last_name">
      <!-- Save, Update, and Delete buttons -->
      <button class="button button-green" @click="save(student)">Save</button>
      <button class="button button-orange" @click="put(student)">Update</button>
      <button class="button button-red" @click="del(student)">Delete</button>
    </div>

  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      apiLog: null,
      students: []
    };
  },
  mounted() {
    // Load students when the component is mounted
    this.read();
  },
  methods: {
    read() {
      // Fetch students from the API
      axios.get(`http://localhost:8000/api/students/read`)
        .then(response => (this.students = response.data))
        .catch(error => console.error('Error loading students:', error));
    },
    save(student) {
      // Save or update a student based on the presence of an ID
      if (!student.id) {
        this.createStudent(student);
      } else {
        this.updateStudent(student);
      }
    },
    createStudent(student) {
      // Create a new student
      axios.post(`http://localhost:8000/api/students/create`, student)
        .then(response => {
          this.apiLog = response.data;
          // Reloads students after creation
          this.read();
        })
        .catch(error => console.error('Error creating student:', error));
    },
    updateStudent(student) {
      // Update an existing student
      axios.put(`http://localhost:8000/api/students/update/${student.id}`, student)
        .then(response => {
          this.apiLog = response.data;
          // Reloads students after updating
          this.read();
        })
        .catch(error => console.error('Error updating student:', error));
    },
    put(student) {
      this.updateStudent(student);
    },
    del(student) {
      // Delete a student
      axios.delete(`http://localhost:8000/api/students/delete/${student.id}`)
        .then(response => {
          this.apiLog = response.data;
          // Reloads students after deletion
          this.read();
        })
        .catch(error => console.error('Error deleting student:', error));
    },
    addNew() {
      // Add a new empty student to the list
      this.students.push({ first_name: '', last_name: '' });
    }
  }
};
</script>

<style lang="css">
#app {
  @apply font-sans text-center text-gray-800;
  margin-top: 60px;
}

.input {
  @apply w-24 p-2 mb-2 ml-1 border border-gray-300 rounded;
}

.input:nth-child(1) {
  @apply w-5;
}

.button {
  @apply py-2 px-2 ml-1 rounded hover:shadow-md transition duration-300 ease-in-out;
}

.button:hover {
  @apply opacity-80;
}

.button-green {
  @apply bg-green-300;
}

.button-orange {
  @apply bg-orange-300;
}

.button-red {
  @apply bg-red-300;
}
</style>