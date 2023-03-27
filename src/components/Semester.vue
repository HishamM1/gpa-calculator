<template>
  <li class="semester-card">
    <ul>
      <SemesterTitle :id="semester.id" @remove-semester="(id) => removeSemester(id)" />

      <!-- Course Inputs -->
      <CourseInputs
        v-for="course in semester.courses"
        :key="course.id"
        :course="course"
        @remove-course="(courseId) => removeCourse(courseId)"
      />
    </ul>
    <SemesterGpa :id="semester.id" :gpa="semester.gpa" />
    <button class="add-course" @click="addCourse">Add Course</button>
  </li>
</template>

<script setup>
import { defineEmits } from "vue";
import CourseInputs from "./CourseInputs.vue";
import SemesterTitle from "./SemesterTitle.vue";
import SemesterGpa from "./SemesterGpa.vue";

const props = defineProps(["semester"]);
const emit = defineEmits(["addCourse", "removeSemester", "removeCourse"]);

const addCourse = () => {
  emit("addCourse", props.semester.id);
};

const removeSemester = (id) => {
  emit("removeSemester", id);
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

.semester-card:not(:last-child) {
  margin-bottom: 20px;
}
</style>
