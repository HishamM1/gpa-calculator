<template>
  <li class="semester-card">
    <ul>
      <div
        style="
          display: flex;
          justify-content: flex-start;
          align-items: center;
          gap: 8px;
          margin-bottom: 10px;
        "
      >
        <span style="font-weight: 600">Semester {{ semester.id }}</span>
        <button class="remove-semester" @click="removeSemester">
          <i class="fa-regular fa-circle-xmark"></i>
        </button>
      </div>
      <!-- Course Inputs -->
      <li class="course-inputs" v-for="course in semester.courses" :key="course.id">
        <input
          class="input-l"
          type="text"
          v-model.trim="course.name"
          placeholder="Course name"
        />
        <select v-model.number="course.grade">
          <option selected disabled hidden value="0">Grade</option>
          <option value="4">A</option>
          <option value="3.666">A-</option>
          <option value="3.333">B+</option>
          <option value="3">B</option>
          <option value="2.666">B-</option>
          <option value="2.333">C+</option>
          <option value="2">C</option>
          <option value="1.666">C-</option>
          <option value="1.333">D+</option>
          <option value="1">D</option>
        </select>
        <input
          type="number"
          v-model.number="course.credits"
          placeholder="Credits"
          class="input-r"
        />
        <button class="remove-course" @click="removeCourse(course.id)">
          <i class="fa-regular fa-circle-xmark"></i>
        </button>
      </li>
    </ul>
    <div class="semester-gpa">
      Semester {{ semester.id }} GPA:
      <h4>{{ semester.gpa }}</h4>
    </div>
    <button class="add-course" @click="addCourse">Add Course</button>
  </li>
</template>

<script setup>
import { defineEmits } from "vue";
const props = defineProps(["semester"]);
const emit = defineEmits(["addCourse", "removeSemester", "removeCourse"]);

const addCourse = () => {
  emit("addCourse", props.semester.id);
};

const removeSemester = () => {
  emit("removeSemester", props.semester.id);
};

const removeCourse = (courseId) => {
  emit("removeCourse", props.semester.id, courseId);
};
</script>

<style scoped>
.add-course {
  border-radius: 10px;
  border: 1px solid var(--third);
  padding: 5px;
  font-size: small;
  background-color: transparent;
  color: var(--third);
  transition: all 0.4s ease;
  cursor: pointer;
  margin-top: 5px;
}

.add-course:hover {
  border: 1px solid var(--secondary);
  color: var(--secondary);
  /* box-shadow: 0px 15px 20px rgba(0, 0, 0, 0.1); */
}
input:focus,
select:focus {
  outline: none;
  border: 1px solid var(--secondary);
}
.remove-semester {
  font-size: 20px;
  border: 1px solid transparent;
  background-color: transparent;
  transition: all 0.4s ease;
  color: var(--third);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}
.remove-semester:hover {
  color: var(--secondary);
}
.input-l {
  border-top-left-radius: 8px 8px;
  border-bottom-left-radius: 8px 8px;
  border: 1px solid var(--third);
  margin-right: -1px;
}
.input-r {
  border-top-right-radius: 8px 8px;
  border-bottom-right-radius: 8px 8px;
  border: 1px solid var(--third);
  margin-left: -1px;
}
input {
  padding: 8px;
  margin: 3px 0px;
}
select {
  border: 1px solid var(--third);
  padding: 7px 8px 7px;
}

.semester-card:not(:last-child) {
  margin-bottom: 20px;
}

.remove-course {
  border: 1px solid transparent;
  background-color: transparent;
  color: var(--third);
  cursor: pointer;
  transition: all 0.4s ease;
  margin-left: 6px;
}

.remove-course:hover {
  color: var(--secondary);
}

.semester-gpa {
  margin: 6px 0px;
}
.semester-gpa h4 {
  color: var(--third);
  display: inline;
}
</style>
